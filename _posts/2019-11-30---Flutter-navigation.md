---
color: rgb(69,190,247)
title: Nawigowanie między ekranami - Navigator
date: "2019-11-30"
layout: "post"
slug: "flutter-navigation"
excerpt: "Niemal każda aplikacja mobilna składa się z wielu ekranów po których wędruje użytkownik w trakcie jej używania. Ekran startowy, lista z przedmiotami, czy detal prezentujący szczegółowe dane o wybranym obiekcie. Dzięki Flutterowi nawigacja jest prosta i przyjazna - wszystko za sprawą wbudowanego Navigatora."
---

Myśląc o aplikacji mobilnej wyobrażasz sobie najprawdopodobniej kafelek na wyświetlaczu telefonu, który możesz stuknąć w celu uruchomienia programu na pełnym ekranie. W zależności od szybkości urządzenia już po chwili widzisz to czego się spodziewasz, czyli aplikację. A właściwie to jej **ekran startowy (początkowy)**.

**Ekran startowy**. Tak właśnie nazwiemy pierwszy widok, który jest nam prezentowany tuż po uruchomieniu się aplikacji. I tak dla *Twittera* będzie to ściana z tweetami, w *Messengerze* lista kontaktów z którymi ostatnio korespondowaliśmy, a w *Uberze* panel do zamówienia podwózki. Oczywista oczywistość.

> Oni to wiedzą Kocie.

Co jednak zrobisz, gdy powiem Ci, że aplikacja składa się z reguły z wielu ekranów, a ten startowy to dopiero początek? Że możesz przechodzić do różnych miejsc przy użyciu przycisków, menu, czy gestów? Weźmy takiego Messengera. Możesz wejść w szczegóły konwersacji, aby ją odczytać, czy napisać nową wiadomość do rozmówcy, a nawet przejść do ekranu z relacjami swoich znajomych.

> ...

Wzruszysz ramionami, lub weźmiesz mnie za durnia, bo przecież **KAŻDA** aplikacja udostępnia możliwość nawigowania między różnymi sekcjami i nie trzeba o tym pisać wstępu. Dobrze się składa, przejdźmy do działania!

![logo.png](/assets/img/blog/navigator/logo.png)

## Navigator

Skoro nawigacja między ekranami jest tak kluczowa dla każdej aplikacji mobilnej, to czy Flutter ułatwia developerem zmierzenie się z tym problemem? Czy może potrzebujemy doinstalować zewnętrzną zależność, która ogra za nas wszelkie trudy? Zamknij **pubspec.yaml**, nie potrzebujemy niczego ponad sam framework, który z pudełka dostarcza widget (a jakże) `Navigator` upraszający cały proces do granic możliwości.

Czym tak właściwie jest `Navigator`? Najprościej mówiąc, jest to widget, który wewnątrz siebie zarządza listą dzieci (widgetów) w formie tzw. **stosu** oraz udostępnia spójny interfejs do wkładania i zdejmowania elementów właśnie poprzez stos. A ten cały stos? To popularna struktura danych (spokojnie!) polegająca na tym, że zaczynamy z "pustym stołem" na który wykładamy (**push**) karty w sposób jedna na drugą, przykrywając je w całości. Gdy stos będzie zawierał kilka kart, jako jego użytkownik mamy dostęp tylko do karty na samym wierzchu. W przypadku gdy chcemy dostać się głębiej - musimy najpierw zrzucić (**pop**) karty powyżej. Ot i cała filozofia stosu.

![logo.png](/assets/img/blog/navigator/stack.png)

> Screen 1 jest na samym dole stosu, a Screen 4 na samej górze co gwarantuje mu to, że jest aktualnie widocznym ekranem. Wkładając nowy ekran znajdzie się on na górze stosu, a jeśli chcielibyśmy ponownie wyświetlić Screen 1 to musimy najpierw pozrzucać w nicość ekrany ponad nim (zaczynając od samej góry).

Stos w Navigatorze różni się od przedstawionego opisu wyłącznie tym, że zamiast kart operujemy ekranami. Wkładamy je i zdejmujemy, zawsze widząc wyłącznie ekran, który znajduje się na samej górze stosu. Ten wrzucony na niego jako ostatni.

Demistyfikację Navigatora i sposobu jego działania możemy uznać za zakończoną. Wiesz już w teorii jak działa i jesteś gotowy na mięcho - praktyczne użycie w aplikacji, które udowodni, że temat nie jest tak płytki jak może się to teraz wydawać.

## Portal

Zacznijmy od początku, czyli od ekranu startowego. Stworzymy w tym celu aplikację o roboczej nazwie **Portal**, którą będziemy rozwijać przez dalszą część wpisu o dodatkowe mechaniki i sposoby nawigowania.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    home: RouteOne(),
  ));
}

class RouteOne extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text("Navigator Demo"),
            RaisedButton(
              child: Text("Push"),
              onPressed: () {},
            ),
          ],
        ),
      ),
    );
  }
}
```

Pamiętasz jak wspominałem o "pustym stole" stosu na starcie? To nie do końca prawda w projekcie pisanym we Flutterze. Użytkownik uruchamiający aplikację widzi przecież ekran z tekstem i przyciskiem, a nie czarną dziurę. Stos nie jest więc wcale pusty! Magia? Technologia! Parametr `home` przekazany do `MaterialApp` wkłada wskazany widget na stos przy starcie, tak by zainicjalizować ekran początkowy. Wygodne, bo po co robić to ręcznie, prawda?

Po uruchomieniu aplikacji powinienieś zobaczyć poniższy widok. Przycisk rzecz jasna nie działa - nie zaprogramowaliśmy co dokładnie ma się wydarzyć po kliknięciu. A skoro tego nie zrobiliśmy to przycisk robi absolutne NIC.

![start_project.png](/assets/img/blog/navigator/start_project.png)

## Naprzód (push)

Podstawową operacją w nawigowaniu jest wepchnięcie nowego ekranu na stos, a nie jego zdjęcie. Dlaczego? Bo żeby coś zrzucić ze stosu, trzeba najpierw to na niego dodać. Prosta logika w moim wykonaniu jest prosta. Co więc uczynimy w tym momencie? Wepchniemy nowy ekran na stos! A ekran to nic innego jak dowolny widget, a skoro dowolny - to nie musimy nawet tworzyć dla niego osobnej klasy. Wepchniemy na stos bezpośrednio zlepek widgetów - tak aby udowodnić że można tam wrzucić dosłownie cokolwiek.

```dart
RaisedButton(
  child: Text("Push"),
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => Center(
          child: RaisedButton(
            child: Text("Pop"),
            onPressed: () {},
          ),
        ),
      ),
    );
  },
)
```

Implementacja wygląda dość oczywiście. Wywołujemy funkcję `Navigator.push`, której zadaniem jak nazwa sugeruje jest wrzucenie nowego ekranu na stos (czyli jego wyświetlenie). Pierwszym parametr to `context`, który jest przekazywany do metody `build` naszego widgeta, drugi natomiast wydaje się bardziej interesujący i mniej zrozumiały. Mieliśmy przecież wrzucić widget, a wcale tak to nie wygląda.

Czyżbym znowu nie powedział całej prawdy o nawigacji? Masz mnie. Navigator zawiera stos ekranów, jednak ekranem jest obiekt typu `PageRoute`, a nie bezpośrednio nasz widget docelowy. Oficjalnie przyjęte nazewnictwo jest takie, że na stosie znajdują się **routes** (ścieżki?), jednak można śmiało zamiennie używać słowa ekran, czy też strona, które są bardziej adekwatne w naszym ojczystym języku.

Skoro "oszustwo" ze ścieżkami zostało wyjaśnione to co tu się właściwie dzieje? Tworzymy nową instancję widgeta `MaterialPageRoute`, który działa na zasadzie tzw. *buildera*. Nie otrzymuje on jak to zwykle bywa parametru `child`, lecz funkcję `builder`, która dynamicznie buduje niezbędne drzewo w odpowiednim momencie (gdy zachodzi proces nawigacji). Zadaniem funkcji jest de facto zwrócenie pełnego drzewa widgetów na nowym ekranie, który za moment zostanie wyświetlony użytkownikowi.

Sam w sobie `MaterialPageRoute` dostarcza jeszcze jedną ważną składową do naszej aplikacji - animację. Ekran nie jest chamsko podmieniany na nowy jak w maszynie stanowej, zamiast tego dostajemy ładną animację przejścia, która dostosowuje się do aktualnej platformy, aby jak najlepiej odwzorować natywne niuanse. Możesz rzecz jasna napisać własną animację przejścia poprzez rozszerzenie bazowego `PageRoute` i cieszyć się w pełni dostosowanym doznaniem, lub ewentualnie skorzystać z wbudowanego `CupertinoPageRoute`, który wymusi animację typu *iOS* na wszystkich dostępnych platformach. Wybór należy do Ciebie, jedyne co trzeba zrobić to podmienić wywołanie `MaterialPageRoute` na własną klasę.

![material_animation.gif](/assets/img/blog/navigator/material_animation.gif)

> Animacja Material w stylu Android

![cupertino_animation.gif](/assets/img/blog/navigator/cupertino_animation.gif)

> Animacja Cupertino w stylu iOS

## Widget per ekran

Mimo tego, że podczas nawigacji możemy wepchnąć dowolne drzewo z widgetami jako nowy ekran to dobra praktyka mówi - nie rób tego. Serio. Zamiast tego stwórz pachnący nowością widget (klasę) i w nim umieśc to co dokładnie chcesz narysować. Tak nakazuje przyzwoitość. Dlaczego? Żeby kod był łatwiej utrzymywalny i czytelniejszy, ale również dlatego, że nigdy nie wiesz z jakiego miejsca w aplikacji będzie można się dostać do danego ekranu w przyszłości. Dzisiaj jest to prosty przycisk, jutro zarząd chciałby dodatkowe menu po lewej stronie, a za miesiąc użytkownik będzie smagał palcem po ekranie rysując odpowiednie gesty nawigacyjne, które zaprowadzą go również do ekranu docelowego.

> Stosuj się do zasady "Nowy ekran, nowy widget", a będziesz szczęśliwszym programistom. Zaufaj mi i całej społeczności Flutterowej.

Wierzę, że dałeś się przekonać do dobrego i jesteś gotowy na drobny refaktoring w kodzie. Stworzymy odseparowany widget na nasz drugi ekran, tak by był łatwo dostępny z dowolnego miejsca w kodzie:

```dart
class RouteTwo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: RaisedButton(
          child: Text("Pop"),
          onPressed: () {},
        ),
      ),
    );
  }
}
```

I oczywiście użyjmy `RouteTwo` zamiast bezpośredniego drzewa podczas przejścia między ekranami:

```dart
RaisedButton(
  child: Text("Push"),
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => RouteTwo(),
      ),
    );
  },
)
```

## Wstecz (natywnie)

Połowa operacji na stosie za nami. Wpychamy na niego nowy ekran, więc umiemy chodzić do przodu. W niektórych aplikacjach może to nawet wystarczyć. Użytkownik nawiguje przed siebie i nie jest zainteresowany tym co już było, lub nawet gdy jest - nie dajemy mu szansy powrotu.

Czy aby jednak na pewno nie możemy się w aktualnej wersji cofnąć do ekranu startowego? Przycisk *Pop*  nie jest w stanie nam pomóc, bo nie został oprogramowany. Musimy jednak pamiętać o systemie operacyjnym uruchamiającym program, który sam z siebie dostarcza mechanizm cofania. Nawet w sytuacji gdy programista sam nie uwzględni takiej możliwości. Dziękuję panie *System Operacyjny* za zrobienie tego za mnie!

Mowa oczywiście o **przycisku cofania** w Androidzie, zarówno tym sprzętowym jak i wirtualnym. Użytkownik zawsze ma możliwość wduszenia strzałki wstecz, która zrzuci aktualny ekran ze stosu i cofnie użytkownika do poprzedniego ekranu, lub jeśli takowego nie ma - zaprezentuje pulpit urządzenia, który jest poza naszą kontrolą. Podobnie sytuacja wygląda na iOS, gdzie cofamy się przy użyciu gestu pociągnięcia za ekran od lewej do prawej.

> Pamiętaj o tym i trzymaj zawsze z tyłu głowy, że nawet gdy nie udostępnisz przycisku cofnięcia to system zrobi to za Ciebie. Zupełnie jak przeglądarka internetowa która również posiada przyciski do nawigowania, bez znaczenia jaką stronę aktualne wyświetla.

## Wstecz (pop)

Nie jesteśmy jednak zdani wyłącznie na cofanie z poziomu systemu. Tworząc interfejs przyjazny użytkownikowi trafimy w końcu na sytuację w której chcemy dać możliwość cofnięcia się przy użyciu własnej kontrolki, lub programowo np. ekran czasowy, ktory dostępny jest tylko przez 5 sekund po których następuje cofnięcie (ponosi mnie fantazja).

Czy tak się da? Nie może by inaczej, wszystko pod programistyczną kontrolą! Nawet sama operacja jest łatwiejsza niż wrzucanie nowego ekranu, bo nie musimy mówić co zrzucamy. Mówimy po prostu - zrzuć ten ekran, zapomnij o nim na wieki.

Zaaplikuj poniższy kod na klasie `RouteTwo`, aby możliwa była pełna nawigacja przy użyciu elementów wewnątrz aplikacji.

```dart
RaisedButton(
  child: Text("Pop"),
  onPressed: () {
    Navigator.pop(context);
  },
)
```

Trudno cokolwiek tutaj objaśniać. Wywołujemy funkcję `Navigator.pop` z aktualnym kontekstem i gotowe. Ekran zostaje zrzucony, uruchamia się animacja, lądujemy na ekranie poprzedzającym. Łatwiej się nie da.

## Ścieżki nazwane

Większe aplikacje zawierające dużą liczbę ekranów rządzą się swoimi prawami. O ile przedstawione sposoby nawigacji są uniwersalne i zadziałają w aplikacji o dowolnej złożoności, to często rezygnuje się ze standardowego `Navigator.push` na rzecz `Navigator.pushNamed`. Wynik działania obu tych funkcji jest identyczny - użytkownik trafia na nowy ekran - różni się jedynie sposób wywołania. Pierwsza funkcja wymaga manualnego utworzenia `PageRoute` wraz z powiązanym widgetem za każdym razem gdy chcemy przeprowadzić nawigację. Druga natomiast potrzebuje wyłącznie nazwy ścieżki do której chcemy przekierować użytkownika.

Spójrz na oba wywołania i sam zdecyduj które jest przyjemniejsze w utrzymywaniu:

```dart
var option1 = Navigator.push(
  context, MaterialPageRoute(
    builder: (context) => RouteTwo(),
  )
);

var option2 = Navigator.pushNamed(context, "/route-two");
```

> Złota zasada programowania: **"Im mniej kodu tym lepiej"**

Zalety `push`:
+ Istnieje
+ Nieznacznie łatwiej przekazywać dane podczas nawigowania

Zalety `pushNamed`:
+ Mniej kodu
+ Brak bezpośredniej zależności (importy) między różnymi widgetami
+ Mniej kodu × każde wywołanie nawigacji
+ Również istnieje

Jeśli więc *pushNamed* jest tak dobry i bezkonkurencyjny to wymieńmy przycisk z pierwszego ekranu, aby skorzystał z wymienionych dobrodziejstw.

```dart
RaisedButton(
  child: Text("Push"),
  onPressed: () {
    Navigator.pushNamed(context, "/route-two");
  },
)
```

Voilà! Przycisk przestał teraz całkowicie działać i nie wykonuje po stuknięciu żadnej akcji. Co do #####? Czyli że niby zapis jest krótszy i w ogóle, ale nie robi tego co powinien? Daj mi chwilę a wszystko wytłumaczę, momencik. Z grubsza chodzi o to, że nawigujemy do ścieżki **/route-two**, ale skąd Flutter ma wiedzieć gdzie to jest? Potrzebujemy skonfigurować drogowskazy do ścieżek dostępnych w obrębie aplikacji.

![signpost.jpg](/assets/img/blog/navigator/signpost.jpg)

Co z tymi drogowskazami? Jak powiedzieć aplikacji, że pod daną ścieżką znajduje się dany widget? Nic prostszego! Wszystkie obsługiwane ścieżki aplikacji należy zdefiniować na poziomie `MaterialApp` poprzez parametr `routes`.

```dart
runApp(MaterialApp(
  home: RouteOne(),
  routes: {
    "/route-two": (context) => RouteTwo(),
  }
));
```

Każdy ekran aplikacji musi zostać zadeklarowany właśnie w obrębie `routes`. Jedynym wyjątkiem jest ekran startowy, który bez naszego udziału rejestruje się pod adresem **/**. Jeśli sam spróbujesz go zarejestrować otrzymasz błąd kompilacji, mówiący o tym, że operacja nie ma sensu, bo framework robi to za Ciebie.

```dart
runApp(MaterialApp(
  home: RouteOne(),
  routes: {
    // The entry below is added automatically by the framework itself
    // do not add it manually to avoid compilation errors
    "/": (context) => RouteOne(),
    "/route-two": (context) => RouteTwo(),
  }
));
```

Automatyczna rejestracja nie jest jednak magiczna i związana jest z parametrem `home`. W przypadku gdy parametr został podany to widget ten będzie wyświetlany dla ścieżki **/**, jednak jeśli go pominiemy to musimy powiedzieć frameworkowi która ze ścieżek jest początkową, poprzez parametr `initialRoute`.

```dart
runApp(MaterialApp(
  initialRoute: "/",
  routes: {
    "/": (context) => RouteOne(),
    "/route-two": (context) => RouteTwo(),
  }
));
```

W taki właśnie sposób odbywa się dodawanie nowych ekranów w sposób nazwany. A jak zrzucić ekran nowym sposobem? `Navigator.popNamed`? Nic z tych rzeczy! Operacja typu *pop* zrzuca aktualny ekran ze stosu, nie musi wiedzieć jaka jest jego nazwa. Zostajemy więc przy starym i sprawdzonym `Navigator.pop`.

## Przekazywanie danych

Jeśli chodzi o sam sposób nawigowania między ekranami to pokryliśmy wszystkie niezbędne aspekty. Do przodu, do tyłu, z małą ilością kodu, bez powtarzania się po całej aplikacji. Czego można chcieć więcej? Cóż, kojarzysz adresy URL w aplikacjach webowych jak np: **/users/16**, który wyświetla profil użytkownika o identyfikatorze *16*? Można zmienić identyfikator na inny dowolny i zobaczyć profil innego użytkownika (o ile istnieje). Dla każdego profilu URL (ścieżka) jest ciut inna, jednak wszystkie kierują na ten sam ekran - różnią się jedynie dane które prezentujemy.

Podobna zależność zachodzi w aplikacjach mobilnych. Weźmy uprzednio wspomnianego Messengera prezentującego listę kontaktów z którymi ostatnio pisaliśmy. Stuknięcie w pozycję kontaktu zabiera nas do konwersacji z daną osobą, a nie do listy wszystkich możliwych wysłanych wiadomości. Ekran szczegółowy otrzymuje informację od ekranu poprzedzającego o identyfikatorze rozmowy którą musi wyświetlić. Przecież Facebook nie jest w stanie zaimplementować całkowicie osobnych ekranów, gdzie jeden będzie prezentował rozmowę z Panią Tereską (pozdrawiam!), a drugi z Panem Krzysiem.

```dart
class BaseChatWidget extends StatelessWidget {}
class ChatTereska extends BaseChatWidget {}
class ChatKrzysio extends BaseChatWidget {}
class ChatMyself extends BaseChatWidget {}
// ...
```

W celu wysłania dynamicznych danych do ekranu posłużymy się tzw. **argumentami**. Jest to obiekt przekazywany jako parametr o nazwie **arguments** do funkcji `Navigator.pushNamed` i parametryzujący ekran o zdefiniowane dane. A czym mogą być dane? Czymkolwiek. Listą, liczbą, ciągiem znaków, obiektem ... Generalnie - wszystkim. Najczęściej jest to  jednak obiekt typu klucz-wartość, którego elastyczność idealnie wpasowuje się w scenariusz nawigacji.

```dart
// Totally valid
Navigator.pushNamed("/route", arguments: [1, 2, 3]);
Navigator.pushNamed("/route", arguments: 1);
// Most popular
Navigator.pushNamed("/route", arguments: {
  "name": "John",
  "age": 16,
});
```

Tak będzie wyglądał nasz nowy przycisk wysyłający użytkownika do drugiego ekranu. Wysyłamy argumenty z imieniem i wiekiem, które docelowo wyświetlimy na nowym ekranie.

```dart
RaisedButton(
  child: Text("Push"),
  onPressed: () {
    Navigator.pushNamed(context, "/route-two", arguments: {
      "name": "Kamil",
      "age": 31,
    });
  },
)
```

Tyle wystarczy jeśli chodzi o wysłanie danych do nowego ekranu. Dostarczamy argumenty, które są następnie automatycznie przekierowywane do punktu docelowego. Wszystko gra, ale pozostaje pewna kluczowa rzecz która chodzi Ci zapewne po głowie. Co z tego, że dane zostały wysłane skoro nie wiadomo jak je odczytać? Co z tego, że wysłałem rakietę w kosmos jeśli nie mam z nią absolutnie żadnego kontaktu? Czas na ...

## Odbieranie danych

... odebranie danych w ścieżce docelowej i zrobienie z nich pożytku. Nawet jeśli nie takiego turbo prawdziwego, to chociaż wyświetlimy dane na ekranie. Tak, żeby udowodnić realność odbioru - w prawdziwej aplikacji zrobisz z danymi co Ci ~~się podoba~~ biznes wymyśli.

Wyciąganie argumentów odbywa się poprzez przydługie wywołanie `ModalRoute.of(context).settings.arguments`. W ten sposób uzyskujemy dostęp do obiektu który przekazaliśmy chwilę wcześniej w zawołaniu `Navigator.pushNamed`. A skoro tak - jesteśmy gotowi do wyświetlenia przekazanych danych na ekranie.

```dart
class RouteTwo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Get the data
    Map<String, dynamic> args = ModalRoute.of(context).settings.arguments;
    final name = args["name"];
    final age = args["age"];

    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            // Display arguments data
            Text("Name: $name"),
            Text("Age: $age"),
            RaisedButton(
              child: Text("Pop"),
              onPressed: () => Navigator.pop(context),
            ),
          ],
        ),
      ),
    );
  }
}
```

## Zakaz powrotu

Ostatnią rzeczą związaną z nawigacją we Flutterze którą chcę zaprezentować jest możliwość reagowania na sytuację w której użytkownik wycofuje się z ekranu. Nie ma znaczenia, czy robi to udostępnionym przyciskiem w aplikacji który pod spodem wywołuje `Navigator.pop`, przyciskiem fizycznym, wirtualnym, czy nawet gestem. Jako programista jesteś w stanie bardzo łatwo zapobiec możliwości cofania się do poprzedniego ekranu - służy do tego widget `WillPopScope`. Jego działanie jest banalnie proste i wymagana jedynie zdefiniowania parametru `onWillPop`, który jest funkcją zwracającą informację, czy cofnięcie może zostać wykonane. Inaczej mówiąc jeśli zdefiniowana przez nasz funkcja zwróci `true` to użytkownik zostanie poprawnie cofnięty, jeśli jednak zwrócimy `false` to nic się nie wydarzy. Użytkownik zostanie na dotychczasowym ekranie.

```dart
WillPopScope(
  onWillPop: async () => false,
  child: Text("No popping!"),
);
```

Czy widget ten ma sens? Po co chcielibyśmy blokować użytkownika na ekranie i nie dać mu możliwości powrotu? Okropny UX! Pełna zgoda, jednak funkcjonalność ta przydaje się do warunkowego opuszczania ekranu jak np:
- możesz z niego wyjść dopiero po upływie X czasu
- po wduszeniu wstecz pojawia komunikat potwierdzający, aby użytkownik nie stracił postępu z obecnego ekranu
- jakikolwiek inny scenariusz

Sprawdźmy w praktyce jak działa `WillPopScope` na prostym przykładzie. Dodamy do aplikacji nowy ekran (route) o nazwie `TrapRoute`, który uwięzi użytkownika na swoich włościach do momentu aż nie kliknie 5 razy wstecz na telefonie. Nie udostępnimy tam żadnego przycisku cofania - niech będzie to prawdziwa pułapka na nieświadomą ofiarę!

```dart
runApp(MaterialApp(
  initialRoute: "/",
  routes: {
    "/": (context) => RouteOne(),
    "/route-two": (context) => RouteTwo(),
    "/trap": (context) => Trap(),
  }
));
```

```dart
class RouteTwo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final args = ModalRoute.of(context).settings.arguments;

    return Scaffold(
      body: Column(
        mainAxisAlignment: MainAxisAlignment.spaceAround,
        crossAxisAlignment: CrossAxisAlignment.stretch,
        children: [
          Text("Name: $args['name']"),
          Text("Age: $args['name']"),
          RaisedButton(
            child: Text("Pop"),
            onPressed: () => Navigator.pop(),
          ),
          // New button for trap route
          RaisedButton(
            child: Text("Trap"),
            onPressed: () => Navigator.pushNamed(context, "/trap"),
          ),
        ]
      ),
    );
  }
}
```

```dart
class Trap extends StatelessWidget {
  int backCount = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: WillPopScope(
          onWillPop: () async {
            backCount++;

            return backCount >= 5;
          },
          child: Image.network(
            "https://rykowski.dev/assets/img/blog/navigator/trap.jpg",
            width: 300,
            height: 250,
          )
      ),
    );
  }
}
```

Pułapka gotowa. Użytkownik po wejściu w ekran musi 5x kliknąć wstecz aby z niego wyjść. Zgodnie z planem, ale nie rób tak w produkcyjnej aplikacji. Użytkownicy nie lubią tego typu niespodzianek i jeśli nie mogą się cofnąć to po prostu zamykają cała aplikację. A tego powstrzymać nie możemy. 
