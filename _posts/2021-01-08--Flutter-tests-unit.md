---
color: rgb(69,190,247)
title: Wstęp do testowania przez testy jednostkowe
date: "2021-01-08"
layout: "post"
slug: "flutter-unit-tests"
excerpt: "Każdy programista o nich słyszał, ale tylko niewielu przekłada je na praktykę. Czy warto? Zdecydowanie tak. Zwłaszcza jeśli chcesz być pewnym swojego kodu i uniknąć regresji w dłuższej perspektywie czasu."
thumbnail: "assets/img/blog/unit_tests/thumbnail.png"
---

Gdybym w eter rzucił hasło *"Pisanie kodu"* to pierwszą myślą przychodzącą ci do głowy jest zapewne zakodowanie nowej funkcjonalności. Ewentualnie naprawa błędu zgłoszonego przez ~~ten upierdliwy~~ dział QA, mimo tego, że z kodu jasno wynika, że aplikacja nie może się tutaj crashować! Istnieje jednak jeszcze jeden aspekt pisania kodu, a mianowicie pisanie **testów**. To ten mityczny twór typu *każdy wie że trzeba to robić, nikt nie ma czasu na głupoty*.

**Czym tak właściwie są te testy?** Niezależną częścią aplikacji (w postaci kodu), która powstaje z boku i sama w sobie nie ma specjalnej wartości dla użytkownika końcowego. Na prawdę nie da się powiedzieć, że testy są integralną częścią aplikacji, nawet jeśli skacze ci teraz ciśnienie z powodu testo-dyskryminacji. Można je pisać, można nie, możesz nawet wyrzucić wszystkie istniejące testy z projektu, a aplikacja nadal będzie działała tak samo. Tak to już jest.

W takim razie po co wkładać jakikolwiek wysiłek w coś czego nie zauważy nikt poza zespołem technicznym? No właśnie po to. Zespół developerski jest głównym beneficjentem istnienia testów. Gwarantują one (nigdy w 100%), że aplikacja działa poprawnie, nawet bez konieczności jej uruchamiania i ręcznego przeklikiwania. Wszystkim zajmuje się automat stwierdzający zero-jedynkowo, czy masz kod pod kontrolą, czy właśnie coś skopałeś dodając nową funkcjonalność, lub refaktorując istniejące już rozwiązanie.

## Ogólnie o testach

Pisanie testów jest łatwe i trudne zarazem. Zależy od której strony na nie spojrzeć i jakimi standardami podążąmy w firmie w której budujemy aplikacje. Pracowałem w projektach betonowych, gdzie pisanie testów nie było mile widziane. Nie mają przecież żadnej wartości, klient nie będzie za nie płacił. Poza tym testy są dla programistów niepewnych swojego kodu, czyli słabych. *Biznesowy beton*. Może być on twoim największym przeciwnikiem w pisaniu testów. Mam jednak dobrą wiadomość! Podejście to mocno odeszło do lamusa w ostatnich latach i nie spotykam się już ze szlabanem na testy. Wzrosła ogólna świadomość odnośnie wytwarzania oprogramowania. Sukces!

Łatwą stroną pisania testów jest natomiast część techniczna. Wystarczy poznać podstawy, a cała reszta idzie dosłownie z górki. Nie ma tutaj dużego pola do manewru, czy nauki. Ot napisać trochę kodu, który jest mocno uniwersalny między wszystkimi dostępnymi technologiami. Zaufaj mi, łatwizna. Zresztą za chwilę będziesz miał okazję zobaczyć sam wszystko w akcji.

### Korzyści z posiadania testów

Dlaczego tak na prawdę programiści chcą pisać testy? Nie dość im kodu w samej aplikacji i mają wymyślną fanaberię na klepanie czegoś z boku? *Otóż nie tym razem*. Korzyści są i to namacalne, nawet jeśli nie przekładające się wprost na samo działanie aplikacji.

1. **Testowanie różnych scenariuszy bez konieczności ręcznego przeklikiwania**. Bo co zrobić, gdy funkcja którą chcesz sprawdzić, jest uruchamiana gdzieś głęboko w aplikacji, a zmiana parametrów z którymi jest wywoływana to minuty stukania po różnych ekranach?
2. **Raz napisany test pozostaje z projektem na zawsze**, a przynajmniej do momentu w którym nie zostanie usunięty podczas większego refaktoringu. Dzięki temu wystarczy jednorazowo napisać test, a wyniku używać do woli.
3. **Możliwość konfiguracji *CI***, aby automatycznie uruchamiał testy podczas zmian w kodzie. Wykryje to potencjalne regresje w aplikacji, nawet gdy bezpośrednio nie dotykałeś danej funkcji.
4. **Spokój ducha i dobry sen**. Nie czujesz przerażenia przed wypuszczaniem aplikacji w świat, bo może nie działać. Na pewno działa, bo testy pilnują cię 24/7. Raczej na *prawie pewno*, bo i tak zawsze znajdą się mniejsze lub większe błędy. Grunt to minimalizacja ryzyka.

Taka porcja zalet chyba wystarczy? Poza tym - zapytaj dowolnego doświadczonego programistę, czy pisanie testów ma sens. Nie ma innej opcji niż odpowiedź twierdząca, nawet jeśli z różnych powodów piszemy ich mniej niż byśmy chcieli. *Życie to nie bajka, ale nie róbmy z niego koszmaru*.

### Typy testów

Test testowi nierówny. Nie będę się zagłębiał w zbędny wywód teoretyczny i opisywał całej rodziny gatunków testów, bo ... nie umiem. Zabawne jest to, ile różnych rodzajów testów istnieje i które trudno jest sklasyfikować programiście nawet z wieloletnim stażem. Najbardziej znanymi i uskutecznianymi w większości firm są **testy jednostkowe** oraz **testy integracyjne**. Generalnie to do szczęśliwości na poziomie 90% wystarczą jedynie te dwa, a resztę pozostawiam purysto-fanatykom. 

#### Testy jednostkowe

Najprostsze. Najłatwiejsze. Produkowane w ilościach hurtowych. Trzon i fundament testów każdej aplikacji. **Polegają na pokryciu pojedynczego fragmentu kodu** - klasy, czy funkcji - przypadkami testowymi. Jak wygląda to w realnym świecie? Przyjmijmy, że masz już własny projekt. Kompiluje się, działa. Robi różne rzeczy. Nagle postanawiasz, że pora dodać trochę testów. Generalnie to lepiej jakby były pisane na bieżąco, ale jest jak jest. Co zrobić? Jak zacząć?

1. Otwórz losowy plik
2. Znajdź w nim funkcję
3. Napisz testy sprawdzające czy funkcja działa zgodnie z oczekiwaniami
4. Powtarzaj do skutku

W wyimaginowanym projekcie natrafiamy na niepozorną funkcję, która zwraca nazwę miesiąca na podstawie dostarczonej daty. Przemilczmy fakt, że istnieją gotowe rozwiązania tego typu i skupmy się na przykładzie.

```dart
String getMonthName(DateTime date) {
  switch (date.month) {
    case 1: return "styczeń";
    case 2: return "luty";
    case 3: return "marzec";
    case 4: return "kwiecień";
    case 5: return "maj";
    case 7: return "lipiec";
    case 8: return "sierpień";
    case 9: return "wrzesień";
    case 10: return "paźdzeirnik";
    case 11: return "listopad";
    case 12: return "grudzień";
  }
}
```

Nie ma testów, bo po co? **Błąd.** Problemy czychają wszędzie, zwłaszcza w najmniej spodziewanych fragmentach kodu. Napiszmy więc testy, aby upewnić się, że wszystko działa jak należy. Bułka z masłem.

Zanim to jednak zrobimy, warto odpowiedzieć sobie na zasadnicze pytanie - gdzie przechowywać pliki z testami? Warto trzymać się domyślnej konwencji, w której każdy nowo utworzony projekt we Flutterze posiada katalog `test`. Osobiście odzwierciedlam niemal 1:1 strukturę plików i katalogów z głównej aplikacji, w taki sposób, że jeśli mam przykładowo plik `lib/ui/routes/home.dart` do wyświetlania ekranu głównego, to mam również jego lustrzane odbicie które przechowuje testy w `test/ui/routes/home_test.dart`. Druga zasada dotyczy nazewnictwa pliku. **Nazwa musi kończyć się na `_test.dart`.** Bez wyjątku. Pamiętaj o tej zasadzie i przypomnij ją sobie, gdy pewnego dnia napiszesz kolejne testy i okaże się, że polecenie do ich uruchamiania całkowicie ignoruje wszystkie przypadki testowe.

Formalności za nami, pora utworzyć plik z testami dla funkcji określającej nazwę miesiąca. Załóżmy, że znajduje się ona w pliku `lib/utils.dart`, więc testy najlepiej zaprogramować w `test/utils_test.dart`. 

```dart
import 'package:flutter_tests/utils.dart';
import 'package:test/test.dart';

void main() {
  group('getMonthName', () {
    test('returns correct name for january', () {
      expect(getMonthName(DateTime(2020, 1, 15)), equals("styczeń"));
    });

    test('returns correct name for february', () {
      expect(getMonthName(DateTime(2020, 2, 15)), equals("luty"));
    });

    // ...
  });
}
```

Przyjrzyjmy się strukturze testów. Po pierwsze importowana jest oficjalna paczka [test](https://pub.dev/packages/test){:target="_blank"}, będąca podstawowym i niezbędnym narzędziem w pisaniu testów. Tak jak do napisania aplikacji mobilnej wymagany jest *Flutter*, tak do testów potrzebna jest paczka *test*.

Druga istotna rzecz to fakt, że każdy plik z testami jest samodzielnym zestawem i wymaga punktu wejściowego w postaci funkcji `main`. Uruchamiając testy to właśnie ta funkcja jest wywoływana, aby ustalić co należy wykonać w następnych krokach. Zupełnie tak jak `main` głównej aplikacji.

Trzeci aspekt to już stricte składnia pisania testów. Funkcja `group` służy jedynie do logicznego zgrupowania wielu pojedynczych testów, które sprawdzają ten sam element systemu, ale pod różnym kątem. Osobiście najczęściej wpisuję tutaj po prostu nazwę klasy, lub funkcji którą będę za chwilę testował. Sprawdza się, zwłaszcza że nic nie stoi na przeszkodzie, aby grupy dowolnie zagnieżdzać w razie potrzeby. W ten sposób łatwo jest zdefiniować grupę nadrzędną dla klasy oraz podgrupy reprezentujące jej pojedyncze metody.

Zdecydowanie ciekawszym elementem są funkcje `test`, odpowiadające za konkretne przypadki testowe. Otrzymują bardziej opisową nazwę, mówiącą co dokładnie dany test sprawdza. Jeśli nie potrafisz zmieścić się w jednej linii z opisem takiego testu - znaczy, że robi za dużo i należy go rozbić na kilka mniejszych! Pamiętaj, że rozmawiamy o testach jednostkowych, a ich zadaniem jest testowanie pojedynczej jednostki, a nie całego układu. Zbyt obszerne testy jednostkowe, mimo że nadal gwarantują działanie systemu to są diabelnie czasochłonne w utrzymywaniu i podatne na częste zmiany. **Unikaj zbyt złożonych testów jednostkowych jak ognia!**

A co siedzi w środku pojedynczego testu? W naszym przypadku są to testy banalne, jednolinijkowe. Nic nie stoi jednak na przeszkodzie, aby linii było więcej - tak się dzieje w prawdziwym świecie oprogramowania. Oczekujemy (`expect`), że wynik zwrócony przez funkcję `getMonthName`, będzie równy (`equals`) określonej wartości. I tak w pierwszym teście wywołujemy naszą funkcję podając jej jako parametr `DateTime(2020, 1, 15)` i oczekujemy że zwrócony zostanie napis `styczeń`. Tylko skąd wziąłem tą datę? Z głowy! Równie dobrze mógłbym podać rok `1988`, albo `2018` - nie ma to znaczenia. Najważniejsze, żeby miesiąc się zgadzał, bo to on jest istotny dla funkcji `getMonthName`.

W ten sposób otrzymujemy 12 testów i z jednej strony jest to super, bo są małe, a z drugiej mocno zahacza o przerost formy nad treścią. Wybór należy do ciebie, ale zdecydowanie wolę mniej kodu niż więcej i chętnie zbiorę to wszystko w pojedynczy test:

```dart
import 'package:flutter_tests/utils.dart';
import 'package:test/test.dart';

void main() {
  group('getMonthName', () {
    test('returns correct names', () {
      DateTime getDate(int month) => DateTime(2020, month, 15);

      expect(getMonthName(getDate(1)), equals("styczeń"));
      expect(getMonthName(getDate(2)), equals("luty"));
      expect(getMonthName(getDate(3)), equals("marzec"));
      expect(getMonthName(getDate(4)), equals("kwiecień"));
      expect(getMonthName(getDate(5)), equals("maj"));
      expect(getMonthName(getDate(6)), equals("czerwiec"));
      expect(getMonthName(getDate(7)), equals("lipiec"));
      expect(getMonthName(getDate(8)), equals("sierpień"));
      expect(getMonthName(getDate(9)), equals("wrzesień"));
      expect(getMonthName(getDate(10)), equals("październik"));
      expect(getMonthName(getDate(11)), equals("listopad"));
      expect(getMonthName(getDate(12)), equals("grudzień"));
    });
  });
}
```

Pokrycie przypadków jest identyczne, sprawdzone zostały wszystkie możliwe miesiące. Skoro nie widać różnicy i test nadal pozostaje czytelny w swojej nowej formie - warto się pochylić nad takim ułatwieniem. Jedyną ceną takiego rozwiązania jest fakt, że przy ewentualnych błędach funkcji `getMonthName` test zostanie przerwany w momencie odnalezienia pierwszego problemu. Nie wykona wszystkich dostępnych instrukcji, więc jeśli funkcja zwracałaby błędny wynik dla lutego i grudnia to test poinformuje nas o błędzie w lutym, a dopiero po jego naprawieniu pojawi się informacja, że grudzień również działa niepoprawnie. W prostych funkcjach jest to kompromis na który można sobie pozwolić, lecz przy bardziej złożonych warto postawić na rozbicie. Tak czy inaczej - życie weryfikuje, które podejścia działa najlepiej dla danego przypadku. Kwestia wprawy.

#### Uruchamianie testów

Testy mamy przygotowane, pora je uruchomić i zweryfikować, czy funkcja `getMonthName` działa zgodnie z założeniami. *Flutter* udostępnia własną komendę do uruchamiania testów, której zadaniem jest uruchomienie wszystkich dostępnych plików z testami (`*_test.dart`) znajdujących się w katalogu `test/`.

```dart
flutter test
```

Po dosłownie sekundzie otrzymujemy wynik ... negatywny. Testy się wysypały z odpowiednimi informacjami dla programisty.

```bash
❯ flutter test
00:01 +0 -1: getMonthName returns correct names [E]
  Expected: 'czerwiec'
    Actual: <null>
     Which: not an <Instance of 'String'>

  package:test_api      expect
  utils_test.dart 14:7  main.<fn>.<fn>

00:01 +0 -1: Some tests failed.
```

Czytanie logów tego typu to codzienność. Wygląda to analogicznie jak wertowanie stosu aplikacji w momencie wystąpienia nieoczekiwanego wyjątku (*stack trace*). Należy przejrzeć komunikat i znaleźć w nim istotne informacje, które naprowadzą nas do naprawienia testu, bądź kodu który właśnie testujemy.

Na samej górze mamy wskaźnik na test, który zakończył się niepowodzeniem. Grupa `getMonthName`, test `returns correct names`. Nie jest to może najistotniejsza informacja w momencie, gdy mamy tylko jeden test na całą aplikację, ale zyskuje na wartości gdy testów powstaje coraz więcej i więcej. Oczywiste.

Sam test twierdzi, że oczekiwał napisu **czerwiec**, a zamiast niego otrzymał bezczelny obiekt **null**. Ma sens, w końcu *czerwiec ≠ null*. Dodatkowo pojawia się informacja, że błąd wystąpił w pliku *utils_test.dart* w linii 14. Spójrzmy, co podejrzanego robi wskazany fragment!

```dart
// ok
expect(getMonthName(getDate(5)), equals("maj"));
// not ok
expect(getMonthName(getDate(6)), equals("czerwiec"));
```

Nie ma tutaj nic podejrzanego. Żadnego kandydata na błąd w teście. Szczególnie, że przypadki dla poprzednich miesięcy zadziałały prawidłowo. Skoro tak - trzeba spojrzeć na właściwą implementację funkcji `getMonthName`, aby dowiedzieć się, dlaczego błędnie obsługuje miesiąc czerwiec.

```dart
String getMonthName(DateTime date) {
  switch (date.month) {
    // ...
    case 5: return "maj";
    case 7: return "lipiec";
    // ...
  }
}
```

Bingo! Zabrakło obsługi na `case 6`. Czyli jednak prawdziwy błąd, który wyłapał za nas test! A teraz wyobraź sobie sytuację, w której napisałeś powyższy kod i używasz go do zaprezentowania obecnej daty użytkownikowi. Jest styczeń - wszystko działa poprawnie. Funkcjonalność trafia na produkcję. Wszyscy o niej zapominają w kolejnych miesiącach. Nagle w nocy z 31 maja na 1 czerwca aplikacja wybucha, przestaje działać stabilnie. W oczach łzy, a myśl tylko jedna.

> Sef nie ksycy, ja nie panimaju.

Sama historia jest nieco podkoloryzowana (ziarno prawdy zasiane na bazie własnych doświadczeń), ale jestem pewien że rozumiesz do czego zmierzam. Po naprawieniu funkcji rzucamy raz jeszcze okiem, czy nie brakuje może dodatkowego *case* i ponownie uruchamiamy testy.

```bash
❯ flutter test
00:01 +0 -1: getMonthName returns correct names [E]
  Expected: 'październik'
    Actual: 'paźdzeirnik'
     Which: is different.
            Expected: październik
              Actual: paźdzeirnik
                           ^
             Differ at offset 5

  package:test_api      expect
  utils_test.dart 18:7  main.<fn>.<fn>

00:01 +0 -1: Some tests failed.
```

Co tym razem? Błąd jakiś inny, ale to dalej nie jest sukces. Zakorkuj szampana, pora wracać do pracy! Test oczekiwał napisu `październik` i właśnie taki dostał - `paźdzeirnik`. Jeszcze próbuje się tłumaczyć, że jest różnica na 5 pozycji (indeksowane od zera). Nawet strzałeczkę w ASCII narysował. Przyglądamy się bliżej i ... jest! Literówka. Kaliber błędu mniejszy niż poprzednio, bo przynajmniej aplikacja nie wybucha, ale to dalej dziura do załatania.

Szybka runda poprawkowa, ponowne uruchomienie testów.

```bash
flutter test
00:01 +1: All tests passed!
```

Wszystkie (słownie jeden) testy przeszły poprawnie. Nie ma więcej błędów, misja zakończona. Można taki kod bezpiecznie dodać do aplikacji i spać spokojnie, bez nocnych telefonów o niespodziewanej awarii. Jakie to uczucie? **Miłe.**

#### Testy integracyjne

Testy jednostkowe są świetne i powinny stanowić kluczowy system obronny przed błędami i regresją w kodzie. Sprawdzają poprawność działania małych, odizolowanych od siebie fragmentów aplikacji. W rzeczywistości to jednak trochę za mało, aby czuć się w pełni bezpiecznym. Co z tego, że repozytoria poprawnie operują na danych, stan odpowiednio reaguje na zadane zdarzenia, czy funkcje pomocnicze dobrze określają nazwę dnia miesiąca, skoro aplikacja jako całość wysypuje się w momencie startu, bądź działa w sposób nieoczekiwany?

Właśnie do testowania aplikacji jako całości służą **testy integracyjne**. Mają za zadanie uruchomić aplikację tak jak robi to prawdziwy użytkownik i zweryfikować jej działanie na żywym organizmie. W przypadku aplikacji mobilnej na urządzeniu fizycznym, bądź wirtualnym.

- Czy da się zarejestrować nowe konto wypełniając formularz z pierwszego ekranu?
- Czy aplikacja zwraca się do mnie po imieniu, które podałem podczas rejestracji?
- Czy mogę na liście produktów odnaleźć daną pozycję i dodać ją do koszyka stukając w odpowiedni przycisk?
- ...

Wszystkie powyższe pytania są słuszne. Nawet pomimo faktu, że testy jednostkowe pokrywają te funkcjonalności. Robią to na dużo niższym poziomie, który nie zawsze odzwierciedla efekt końcowy w postaci aplikacji dostarczanej użytkownikowi.

O testach integracyjnych we *Flutterze* przeczytasz w [następnym wpisie](/blog/flutter-driver-tests/), który jest dedykowany właśnie temu konkretnemu zagadnieniu. Framework mocno nas rozpieszcza i również tym razem dostarczy gotowe i wbudowane rozwiązanie.

## Podsumowanie

Pisanie testów nie jest procesem skomplikowanym. Powtarzalnym? Owszem. Trzeba się jednak przemóc i stopniowo wdrażać testy, szczególnie do krytycznych elementów systemu, których działanie jest złożone, a testowanie ręczne trudne, bądź czasochłonne. W takim przypadku raz napisane testy, bardzo szybko się zwrócą, a ty nie musisz przy każdej drobnej zmianie sprawdzać, czy nic po drodze nie wybuchło. Poczucie komfortu przy pracy z otestowanym projektem jest nieporównywalnie wyższe, niż gdy takowych brak. Nie zatracaj się jednak w pisaniu testów jako *sztuka dla sztuki* i nie testuj na siłę każdej jednej linii w kodzie. Znajdź umiar i równowagę, a testy staną się twoim przyjacielem.

Kod pełnej aplikacji znajdziesz na [GitHubie](https://github.com/vintage/flutter_demo_unit_tests).

A jak pisanie testów wygląda w twoim projekcie? Napisałeś, dopiero zaczynasz, czy może nie wierzysz w ich pozytywny wpływ na życie projektu? **Daj znać!**