---
color: rgb(69,190,247)
title: Testy integracyjne z Flutter Driverem
date: "2021-01-15"
layout: "post"
slug: "flutter-driver-tests"
excerpt: "Posiadanie wyłącznie testów jednostkowych nie daje nam pełen gwarancji działania systemu. Jak więc go przetestować, aby spać spokojnie? Integracyjnie wraz z wbudowanym narzędziem Flutter Driver."
thumbnail: "assets/img/blog/driver_tests/thumbnail.png"
---

W [poprzednim wpisie](/blog/flutter-unit-tests/) rozpoczęliśmy temat pisania testów oraz dlaczego warto zacząć to robić we własnych projektach. Tematem przewodnim były testy jednostkowe. Stanowią one główny filar zapewniający programistę, że projekt działa i zachowuje się w sposób oczekiwany. Jest to świetny krok w rozpoczęciu przygody testowej, ale zdecydowanie nie ostatni. **Testy jednostkowe są mocno oderwane od tego, co prawdziwy użytkownik robi w aplikacji**. A co takiego czyni?

Zdecydowanie nie tworzy bezpośrednio repozytoriów, modeli, czy worków na stan. Nie wywołuje funkcji realizujących logikę biznesową. Prawdziwy użytkownik z krwi i kości stuka w przyciski. Wypełnia formularze poprzez wirtualną klawiaturę. Przewija listy w celu znalezienia interesującej go pozycji. Czyta, bądź chociaż pobieżnie przegląda wyświetlaną treść. Czy brzmi to jak użytkownik, którym sam jesteś korzystając z aplikacji zainstalowanych na telefonie? Też nim jestem gdy kończę pracę. Każdy jest.

Skoro testy jednostkowe nie zostały stworzone do bycia użytkownikiem, to cóż począć? Przeskoczyć na przeciwległy brzeg pisania testów! Tak jak testy jednostkowe utożsamiane są z najniższym możliwym poziomem, tak najwyższym z nich są ... **testy integracyjne**.

## Testy integracyjne

Kluczowym zadaniem testów integracyjnych jest sprawdzenie, czy aplikacja działa poprawnie jako produkt końcowy. Mają odpowiedzieć na proste pytanie. **Czy użytkownik aplikacji po jej uruchomieniu, otrzyma oczekiwany rezultat?** Wszystko to przy założeniu, że możliwości testu integracyjnego są mocno ograniczone. Niemal do tych samych funkcjonalności, które posiada żywa persona. **Pełna symulacja**.

Testy są całkowicie niezainteresowane szczegółami implementacyjnymi leżącymi pod spodem. *Provider*, *Bloc*, czy *MobX*? Dane zapisują się w chmurze, czy na urządzeniu? *Firebase*, a może własny serwer? O ile przy testach jednostkowych ma to znaczenie (można je minimalizować!), to testy integracyjne nawet nie chcą wiedzieć jakie spaghetti siedzi w kodzie. Aplikacja ma tak po ludzku działać. Zapomnij o widgetach, stanie, nawigacji, przekazywaniu kontekstu. Są to rzeczy o których użytkownik nie ma pojęcia, więc test integracyjny również nie wie o czym do niego mówisz. **Pełna izolacja**.

### Flutter driver

Tworzenie testów integracyjnych stanowi część Fluttera, otrzymujemy więc oficjalną paczkę w postaci [flutter_driver](https://api.flutter.dev/flutter/flutter_driver/flutter_driver-library.html){:target="_blank"}. Jest to dedykowane narzędzie do budowania scenariuszy testowych, uruchamianych zarówno na emulatorach jak i fizycznych urządzeniach. Zgadza się. Scenariusze testowe wykorzystywane są do pokierowania wirtualnego użytkownika przez uprzednio zbudowaną aplikację. Naturalnie wymusza to pewien mankament testów integracyjnych. **Powolność**. O ile test jednostkowy wykonywany jest w ułamku sekundy, tak jego integracyjny odpowiednik potrzebuje zdecydowanie więcej czasu. Zanim rozpocznie swoją pracę, niezbędne jest zbudowanie świeżej aplikacji. Co w zależności od jej rozmiaru i złożoności potrafi zająć dłuższą chwilę.

Jako że natura pisania testów integracyjnych, wymaga posiadania gotowej aplikacji, posłużę się domyślnym projektem utworzonym przez `flutter create`. Został on delikatnie rozszerzony o wstępny ekran logowania, aby zapobiec nieautoryzowanemu dostępowi i lepiej poznać mechanikę pisania testów. Kod aplikacji znajdziesz na moim GitHubie [vintage/flutter_demo_integration_tests](https://github.com/vintage/flutter_demo_integration_tests), a poniżej załączam zrzut prezentujący pełnię jej możliwości.

![demo_app.png](/assets/img/blog/driver_tests/demo_app.png)

### Konfiguracja

Nowo utworzony projekt nie posiada domyślnie zależności dla testów integracyjnych. Pierwszą więc rzeczą jaką należy zrobić jest dodanie wpisu w *pubspec.yaml*.

```yaml
dependencies:
  flutter:
    sdk: flutter

  flutter_driver:
    sdk: flutter
  test: any
```

Poza samym driverem potrzebujemy również standardowej paczki do pisania testów. Testy integracyjne to wciąż testy, prawda? Racja.

Ostatnim elementem przygotowawczym jest utworzenie plików konfiguracyjnych do przechowywania scenariuszy testowych. Dobrze przyjęta konwencja zakłada, że wszystko co związane jest z testami integracyjnymi znajduje się w katalogu `test_driver`. W ten sposób bardzo dużo aplikacji na starcie rozgałęzia się na trzy podstawowe katalogi:

- `lib/` - kod aplikacji wraz z logiką biznesową i wszystkim co jest z nią związane
- `test/` - testy jednostkowe, widgetów oraz wszystkie inne które nie są integracyjnymi
- `test_driver/` - testy integracyjne kierowane przez drivera

Korzystanie z dobrodziejstw drivera wymaga dwóch plików. Pierwszy z nich należy do grupy *stwórz i zapomnij* i definiuje wyłącznie jak uruchomić aplikację testową. W najprostszym wariancie wywołujemy po prostu funkcję `main` z głównej aplikacji i nigdy tu nie wracamy. Nie jest tak jednak zawsze i może okazać się, że chcemy przykładowo wyłączyć całkowicie analitykę, reklamy, lub dowolną inną funkcjonalność, która nie ma sensu z punktu widzenia testu integracyjnego. Apeluję jednak o ostrożność. Każde takie dostosowanie kodu, oddala drivera od wersji aplikacji, którą dostaje prawdziwy użytkownik

Wybieramy wariant prosty tworząc plik `test_driver/app.dart`.

```dart
import 'package:flutter_driver/driver_extension.dart';
import 'package:flutter_demo_integration_tests/main.dart' as app;

void main() {
  enableFlutterDriverExtension();
  app.main();
}
```

Same testy definiowane będą w powiązanym pliku `test_driver/app_test.dart`. Nazwa nie jest przypadkowa, a budujemy ją poprzez dodanie przyrostka `_test` do poprzedniego pliku. Zawsze definiując testy integracyjne konieczne jest trzymanie się reguły: *dwa pliki, przyrostek _test*. Chociaż szczerze powiedziawszy to nie pracowałem jeszcze przy aplikacji, która miałaby więcej niż jedną parę plików. Zawsze wystarcza pojedyncza para w postaci `app.dart` i `app_test.dart`.

Dodajemy więc do projektu plik `test_driver/app_test.dart`.

```dart
import 'package:flutter_driver/flutter_driver.dart';
import 'package:test/test.dart';

void main() {
  group('App', () {
    FlutterDriver driver;

    setUpAll(() async {
      driver = await FlutterDriver.connect();
    });
    tearDownAll(() => driver?.close());

    test('full flow', () async {
      // Test logic
    });
  });
}
```

Jest to typowy szablon testu integracyjnego, który korzysta z analogicznych mechanizmów jak testy jednostkowe. Funkcja `setUpAll` uruchamiana jest jednorazowo dla całego scenariusza, przed uruchomieniem pierwszego testu. Jej zadaniem jest nawiązanie połączenia z driverem, w końcu bez tego nie jesteśmy w stanie nic przetestować. `tearDownAll` jest jej przeciwieństwem. Uruchamiany jest automatycznie po zakończeniu pracy ostatniego testu i zamyka uprzednio nawiązane połączenie z driverem.

Ciekawszą rzecz stanowi kod w obrębie funkcji `test`. W sensie będzie ciekawszy jak już się za moment pojawi, bo póki co świeci pustką. Sama konfiguracja jest już jednak zakończona, pora ruszać dalej.

### Uruchamianie testu

W celu weryfikacji można już bez żadnych problemów uruchomić przygotowany zestaw. Co prawda niczego jeszcze nie testujemy, ale warto upewnić się, że konfiguracja przebiegła poprawnie. W celu uruchomienia drivera skorzystamy z polecania `flutter drive`, któremu wskażemy gdzie znajduje się zestaw testowy.

```bash
❯ flutter drive --target=test_driver/app.dart         29s

Using device sdk gphone x86.
Starting application: test_driver/app.dart
Installing build/app/outputs/flutter-apk/app.apk...
Running Gradle task 'assembleDebug'... Done
✓ Built build/app/outputs/flutter-apk/app-debug.apk.
Installing build/app/outputs/flutter-apk/app.apk...
00:00 +0: App (setUpAll)

00:01 +0: App full flow

00:01 +1: App (tearDownAll)

00:01 +1: All tests passed!

Stopping application instance.
```

W logach możemy przeczytać, że wybranym urządzeniem do testów został *sdk gphone x86* (emulator), pojawiają się typowe informacje dotyczące budowania aplikacji. Gdy aplikacja została zainstalowana na telefonie, uruchomione zostały w następującej kolejności: *setUpAll*, test *full flow* oraz *tearDownAll*. Końcowy komunikat świadczy o tym, że testy zakończyły się sukcesem, acz skoro nic nie robią to trudno było oczekiwać innego rezultatu.

Polecenie do uruchamiania testów jest w pełni konfigurowalne. Wszystkie dostępne parametry takie jak wskazanie urządzenia, czy ustawienie zmiennych środowiskowych znajdziesz pod `flutter drive --help`.

### Tworzenie testu

Pora wrócić i zaimplementować właściwy test. Wszystko opiera się na tym, aby przy pomocy dostępnych funkcji instruować drivera jaki następny krok powinien podjąć. Scenariusz przez nas pisany rozpoczyna się w momencie, w którym użytkownik uruchomił aplikację i widzi jej pierwszy ekran. Do dzieła!

Wcześniej o tym nie wspomniałem, ale pierwszy ekran aplikacji jest specyficzny. Formularz logowania widoczny jest dopiero po przewinięciu ekranu w dół, a domyślnie widoczne są bloczki z odcieniami szarości. Pierwszym naszym testem będzie więc zmuszenie drivera, aby właśnie poprzez przewijanie ekranu odnalazł właściwy formularz.

> Interakcje z elementami aplikacji są możliwe tylko wtedy, gdy widoczne są one na ekranie. Nie można powiedzieć driverowi aby stuknął przycisk, który jest narysowany na samym dole przewijalnej listy. Tak samo jak użytkownik nie widzi przycisku bez przewinięcia, tak driver nie jest go świadomy.

![demo_app_scroll.gif](/assets/img/blog/driver_tests/demo_app_scroll.gif)

Pierwszą rzeczą do zrobienia jest powiedzenie driverowi jak odnaleźć właściwe elementy ekranu. Użytkownik widzi je oczami, driver takiej mocy nie posiada. Musimy przewinąć ekran, potrzebny jest więc uchwyt listy, a także uchwyt do formularza którego szukamy. Operacje wyszukiwania elementów są realizowane przez globalnie zaimportowany obiekt `find`. Dostarcza on kilka sposobów wyszukiwań, gdzie najczęściej stosowane to:

- `.byType` do szukania po nazwie klasy
- `.byValueKey` do szukania po nazwie klucza (*key*)
- `.text` do odnalezienia widgetu **Text** wyświetlającego konkretny napis

> Wywołanie metody obiektu `find` nie przeprowadza żadnego wyszukiwania. Jest to meta informacja dla drivera z instrukcją jak dany obiekt odnaleźć. Jeśli *widget* jest skarbem, to obiekt zwrócony przez *find* jest mapą do skarbu, a *driver* piratem który chce do niego trafić.

```dart
test('full flow', () async {
  final scrollable = find.byType("ListView");
  final loginForm = find.byValueKey("myForm");

  await driver.scrollUntilVisible(
    scrollable,
    loginForm,
    dyScroll: -200,
  );
})
```

Uchwyty zdefiniowane. Pora rozpocząć przewijanie listy, aż do momentu gdy wyświetlony zostanie poszukiwany formularz. Posłużymy się wygodnym `scrollUntilVisible`, któremu powiemy jaki element i w którą stronę przewijać (wartość ujemna oznacza w tym wypadku dół) oraz czego szukamy. Gotowe. Czy aby jednak na pewno? Test jednostkowy sprawdza wynik zero-jedynkowo, a jak jest tutaj?

Przewijanie listy trwa do momentu odnalezienia wybrańca. Jak długo? Kiedy test zorientuje się, że wybrańca zabrakło na ekranie? Aspektem, który warto trzymać z tyłu głowy pisząc testy integracyjne jest **czas**. Każda operacja w driverze potrzebuje czasu, aby stwierdzić czy wszystko jest pod kontrolą. To dlatego wszystkie udostępniane interakcje z driverem posiadają konfigurowalny **timeout**, którego przekroczenie oznacza przerwanie całego testu z wynikiem negatywnym. Domyślnie nie jest ustawiony, acz nie oznacza to, że operacja będzie trwała w nieskończoność. Każdy pojedynczy test również posiada zadeklarowany czas życia, który domyślnie wynosi zaledwie 30 sekund. Przekroczenie tego czasu wiąże się z błyskawicznym jego przerwaniem i wystawieniem negatywnego wyniku. Parametr jest w pełni konfigurowalny i jeśli potrzebujesz więcej czasu w samym teście - nic nie stoi na przeszkodzie.

```dart
test('full flow', () async {}, timeout: Timeout(Duration(days: 1)))
```

Dodajmy *timeout* do wyszukania formularza na ekranie. Jeśli w ciągu 3 sekund nie uda się go odnaleźć - przerwij test, nie ma sensu szukać go dłużej.

```dart
driver.scrollUntilVisible(
  scrollable,
  loginForm,
  dyScroll: -200,
  timeout: Duration(seconds: 3),
);
```

Formularz odnaleziony, test przechodzi. Następny krok? Trzeba się zalogować, czyli wypełnić formularz danymi testowymi. Analogicznie jak poprzednio musimy:

1. `find` - wyznaczyć niezbędne elementy ekranu
2. `driver` - wykonać z ich udziałem adekwatne operacje

```dart
final usernameInput = find.byValueKey("username_input");
final passwordInput = find.byValueKey("password_input");
final submitButton = find.text("Login");

await driver.tap(usernameInput);
await driver.enterText("test");
await driver.tap(passwordInput);
await driver.enterText("12345");
await driver.tap(submitButton);

await driver.waitFor(find.text("Wrong credentials!"));
```

Mądry ten driver, zachowuje się zupełnie jak prawdziwy użytkownik. Krok po kroku. Do celu.

1. Stuknij w pole nazwy użytkownika, aby stało się aktywne (złapało focus)
2. Podaj nazwę użytkownika
3. Wybierz następne pole
4. Wprowadź hasło
5. Stuknij w przycisk zatwierdzający
6. Na sam koniec potwierdź, że na ekranie widoczny jest komunikat o błędnych danych

![auth_failed.png](/assets/img/blog/driver_tests/auth_failed.png)

Poprawne dane logowania to *admin:12345*, wystarczy więc poprawić nazwę użytkownika i wysłać formularz ponownie ...

```dart
await driver.tap(usernameInput);
await driver.enterText("admin");
await driver.tap(submitButton);
```

... aby test przeszedł do kolejnego ekranu, który również warto zautomatyzować.

![auth_passed.png](/assets/img/blog/driver_tests/auth_passed.png)

Nie ma na nim jednak niczego, czego nie posiadał ekran poprzedni! Jest tekst, przycisk. Nuda. Nic odkrywczego, nie zatrzymujemy się tutaj, bo szkoda czasu. Przykładowy test sprawdzający trzykrotne stuknięcie w przycisk:

```dart
await driver.waitFor(find.text("0"));

final incrementButton = find.byType("FloatingActionButton");
await driver.tap(incrementButton);
await driver.tap(incrementButton);
await driver.tap(incrementButton);

await driver.waitFor(find.text("3"));
```

## Podsumowanie

Pisanie testów z driverem jest nieraz nawet łatwiejsze, niż poprawne zbudowanie pełnego scenariusza jednostkowego. Nie trzeba się zastanawiać jak zamockować usługi, jak przeprojektować klasy, żeby testowanie było bardziej wyizolowane. Jest gotowa aplikacja dla której wymyślasz kroki i powoli je realizujesz. Nie trzeba nawet dobrze znać dziedziny domenowej, czy kodu aplikacji, aby pchnąć do przodu temat testów integracyjnych. Bajka.

Trzeba jednak pamiętać, że ich uruchomienie jest powolne, więc testowanie wszystkich możliwych przypadków jest niemożliwe, lub co najmniej mocno ograniczone. Nie stanowią one również swoistego kontraktu samego kodu. Tak jak test jednostkowy jest w stanie wykazać spaghetti w kodzie i nieintuicyjny interfejs klasy, tak test integracyjny nie ma szans tego wyłapać. Oba rodzaje testów świetnie się uzupełniają i warto je posiadać w swoich projektach. Nawet jeśli z reguły posiadasz tylko jeden test integracyjny na pokrycie całej aplikacji i nie rozbijasz go na mniejsze fragmenty (to ja!).

