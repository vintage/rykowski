---
title: Asynchroniczny Dart - Event Loop, Futures, async/await
date: "2020-06-27"
layout: "post"
slug: "async-dart-event-loop-futures"
excerpt: "Dart jako język jednowątkowy jest w stanie wykonywać tylko jedną operację w danym czasie. Jedna za drugą. Brzmi powolnie? Nic z tych rzeczy! Dowiedz się jak działa Event Loop i kod asynchroniczny."
---

Pisząc dowolną aplikację we Flutterze posługujesz się językiem Dart. Wszystkie ograniczenia i subtelności zdefiniowane w samym języku, będą ci również towarzyszyły we Flutterze. Nie da się inaczej. Flutter stoi na Darcie, koniec i kropka. Czy to dobrze? Jasne, czemu nie. Lubię ten język i mam do niego pewnego rodzaju słabość, ale warto poznać go czasem nieco głębiej poza samą jego składnią.

Nie będziemy jednak wertować całego języka od podstaw, od tego jest oficjalna dokumentacja. Skupimy się jedynie na tym **skąd Dart wie który kod uruchomić w jakim czasie** oraz na czym opiera się cała magia asynchroniczności. Temat jest bardzo szeroki i wymaga zrozumienia kilku podstawowych konceptów od których rozpoczniemy dzisiejszy wpis.

![logo.png](/assets/img/blog/async/logo.png)

# Jednowątkowy Dart

Tak jest, zgadza się. **Dart jest językiem jednowątkowym**, tak jak wiele innych popularnych języków programowania jak chociażby *JavaScript*, czy *Python*. A co to tak na prawdę oznacza?

> Jakieś wątki pewnie i że jest jeden?

Bingo! Z samej nazwy można praktycznie wszystko wywnioskować. Aplikacja pisana w Darcie wykonuje wszystkie swoje operacje w obrębie pojedynczego wątku systemowego. A skoro wątek jest tylko jeden, to program może robić tylko jedną rzecz w danym punkcie czasu. Wykona jedną operację, później drugą, trzecią itd. Nie ma tutaj mowy o żadnym zrównoleglaniu prac żeby w tym samym momencie robić rzeczy A, B i C. Zapomnij.

Pierwsze wyobrażenie o takim sposobie wykonywania operacji przynosi obraz powolności. No bo jak to tak? Nie można robić wielu rzeczy na raz? Przecież ŻADNA aplikacja nie może tak działać. To się nie może udać! A jednak się udaje i aplikacje pisane we Flutterze bez problemów osiagają mityczne 60 klatek na sekundę. Jak to się dzieje? O tym już za chwilę.

Sam aspekt jednowątkowości ma z mojej perspektywy dwie zasadnicze zalety. **Pierwszą** jest to, że aktualnie wykonywany kod nie może zostać przez nic przerwany. Tylko jeden wątek ma dostęp do pamięci, więc tylko on steruje tym co się wewnątrz dzieje. Nikt z zewnątrz nie może nagle wskoczyć i zmienić czegokolwiek, jak chociażby wartości obiektów. **Drugą** natomiast zaletą jest brak deadlocków i wyścigów w dostępie do pamięci, gdy w dokładnie tym samym momencie jest kilku zainteresowanych danym obszarem jak np. zmiana wartości współdzielonej zmiennej.

> Jeden wątek to w praktyce mniej zmartwień.

```dart
import "package:flutter/foundation.dart";

bool flag = true;

void main() {
  print("start main");
  print(randomSort([2,1,3,4,5,6,7,8,9,10]));
  print("end main, flag: $flag");
}


List<int> randomSort(List<int> numbers) {
  final easy = [...numbers]..sort();
  final sorted = [...numbers]..shuffle();
  
  while (!listEquals(easy, sorted)) {
    sorted.shuffle();
  }
  
  return sorted;
}
```

Powyższy kod gwarantuje, że wszystkie instrukcje począwszy od pierwszego printa, przez dziwny algorytm sortowania, aż po ostatniego printa zostaną wykonane jedno po drugim. Co więcej - żaden inny kod nie zostanie uruchomiony w między czasie, bez znaczenia jak bardzo byśmy wszystko skomplikowali. Znaczy to tyle, że zmienna **flag** na pewno będzie miała wartość **true**, bo żaden inny kod nie ma prawa być uruchomiony do momentu zakończenia wszystkich **synchronicznych** operacji.

# Synchroniczność

Wspomniane operacje **synchroniczne** to model ich wykonywania w którym każde kolejne zadanie następuje bezpośrednio po sobie i nie ma w nim miejsca na żadne działania dodatkowe. Przez zadanie mam na myśli pojedynczą instrukcję, dla uproszczenia możemy przyjąć że jest to po prostu jedna linia kodu.

1. Uruchamiamy zadanie #1
2. Przetwarzamy je i uzyskujemy wynik
3. Uruchamiamy zadania #2
4. Przetwarzamy je i uzyskujemy wynik

...

n. Uruchamiamy ostatnie zadanie, przetwarzamy i uzyskujemy wynik

Jest to model którego używamy w większości miejsc aplikacji, łatwo można prześledzić co i kiedy jest wykonywane. No bo skoro wykonuje się teraz linia *#45*, to następna musi być *#46*. Nie ma innej możliwości, łatwizna.

Ma on jednak zasadniczą wadę, a mianowicie szansę na zablokowanie programu podczas oczekiwanie na wolne zdarzenie takie jak odczyt danych z sieci, czy nawet lokalnego pliku. Chyba nie chcesz zawiesić całej aplikacji na czas gdy odpytujesz swoje API? Jeden wątek, jedno zadanie, a czekanie to też praca w modelu synchronicznym.

Gwarantuję ci, że użytkownik nie będzie wyrozumiały i jest mało zainteresowany faktem jednowątkowości. Ma być szybko i responsywnie! Czyli jak? **Asynchronicznie!**

---
> Idziesz do kina na swój ulubiony film. Ustawiasz się w kolejce po bilet i czekasz aż wszystkie osoby przed tobą zostaną obsłużone i zostanie wydany im bilet. Teraz pora na ciebie, możesz kupić bilet i cieszyć się seansem.

# Asynchroniczność

Kod napisany w sposób **asynchroniczny** różni się od swojego synchronicznego odpowiednika tym, że nie jest w pełni wykonywany "ciągiem". A przynajmniej nie ma takiej gwarancji. Funkcja asynchroniczna ma możliwość oddania sterowania i wykonywania operacji do innego miejsca w kodzie. Mechanizm ten nie odbywa się oczywiście w sposób losowy i to my pisząc kod wskazujemy na konkretne miejsca, które w czasie oczekiwania na **COŚ** mają zwolnić wątek i dokończyć zaplanowane zadania w momencie gdy to **COŚ** będzie już gotowe.

> Czekanie to nie praca. Odpoczywasz? Daj szansę innym zrobić coś produktywnego!

Czym będzie to **COŚ** na prawdziwym przykładzie? W dzisiejszych aplikacjach są to najczęściej operacje wejścia-wyjścia (I/O), które z natury nie wymagają udziału procesora, więc mogą go zwolnić dla bardziej potrzebujących:
- obsługa API, bez znaczenia czy to REST API, GraphQL, czy jeszcze coś innego. Istotnym punktem jest to, że zachodzi tutaj ruch sieciowy.
- odczyt/zapis lokalnych plików, gdzie praca wykonywana jest przez urządzenie dyskowe, a nie procesor.

Sama asynchroniczność nie ogranicza się jednak do wyżej wymienionych i po prawdzie to każda operacja może być wykonana w taki właśnie sposób.

Pamiętaj przy tym o jednej bardzo, ale to bardzo ważnej rzeczy. Dart nadal pozostaje jednowątkowy! To, że zaplanujesz kod do wykonania asynchronicznego nie znaczy, że będzie wykonywał wiele operacji w jednym czasie. Dalej na poziomie procesora jest ograniczony do *"jedna rzecz w jednym czasie"*, a cała magia asynchroniczności opiera się na wewnętrznej kolejce zdarzeń zwanej **Event Loop**.

---
> Idziesz do restauracji na swoje ulubione naleśniki. Lokal jest już w połowie zapełniony ludźmi którzy czekają na swoje zamówienia, a mimo to kelner podchodzi i przyjmuje twoje. Co więcej, wcale nie musisz czekać aż dania zostaną wydane wszystkim którzy zamówili wcześniej. Nie blokuje cię więc pan ze stolika #2, który poprosił o pieczonego prosiaka którego przygotowanie zajmie ponad 2h. Ma to sens, prawda?

# Event Loop

Uruchamiając dowolny program Darta, a więc m.in. aplikację Flutterową, system tworzy nowy wątek w obrębie którego wykonywane będą wszystkie zaprogramowane operacje. W tym właśnie wątku będzie alokowana cała niezbędna pamięć dla twoich obiektów, której nie będziesz mógł współdzielić z żadnym innym wątkiem. Zasada jednowątkowości.

W ramach startowania aplikacji, jedną z pierwszych rzeczy która się wydarzy jest inicjalizacja wewnętrznej pętli zwanej **Event Loop**, która będzie trwała tak długo jak żyje sam wątek. To właśnie ten element układanki jest odpowiedzialny za kolejność w której uruchamiany jest kod programu, szczególnie gdy rozmawiamy o jego asynchronicznej odmianie.

```dart
void eventLoop() {
  while (true) {
    final microtask = popMicrotaskFromQueue();

    if (microTask) {
      run(microTask);
    } else {
      final event = popEventFromQueue();

      if (event) {
        run(event);
      }
    }
  }
}
```

Event Loop zarządza swoim stanem w formie dwóch bliźniaczo podobnych kolejek FIFO (**F**irst **I**n **F**irst **O**ut), gdzie zadania wykonywane są w takiej kolejności w jakiej zostały do niej dodane.

## Microtask Queue

Kolejka o wysokim priorytecie do zadań specjalnych, których najpewniej nie będziesz potrzebował przy budowaniu aplikacji. Szczerze powiedziawszy to nigdy nie użyłem jej produkcyjnie, nie było takiej potrzeby. Jej głównym zastosowaniem jest zaplanowanie wykonania pewnego fragmentu kodu asynchronicznie, ale przed wywołaniem jakiegokolwiek kodu umieszczonego na *Event Queue*.

```dart
void main() {
  print("start main");
  scheduleMicrotask(() => print("micro"));
  print("end main");
}
```

Po uruchomieniu powyższego kodu zobaczysz następującą kolejność w konsoli:

```
1. start main
2. end main
3. micro
```

Napis *micro* jest na pozycji #3, a nie #2, ponieważ jest wykonywany asynchronicznie. Wywołujemy funkcję `scheduleMicrotask`, która jako parametr otrzymuje funkcję, która jest niezwłocznie umieszczana na kolejce, ale nie wykonywana! Dopiero, gdy wykonają się wszystkie operacje synchroniczne, *Event Loop* decyduje o uruchomieniu kolejnego zadania z kolejki, którym jest właśnie ostatni print.

## Event Queue

Kolejka o standardowym priorytecie, która zawsze ma dużo pracy i zadań do wykonania. To właśnie tutaj odbywa się większość operacji asynchronicznych każdej aplikacji mobilnej. Na tym poziomie obsługiwane są takie zdarzenia jak gesty użytkownika, odpowiedzi na żądania HTTP, czy timery. Użytkownik stuknął palcem w przycisk? *Event*. Użytkownik skroluje listę? Dużo *eventów*. Przyszła w końcu wyczekiwana odpowiedź z serwera na którą tak długo czekałeś? *Event*. I tak bez końca.

Jak wykonać kod na tej kolejce? Może jakiś analogiczny `scheduleEvent`? Byłoby spójnie, ale interfejs jest delikatnie inny. W celu zaplanowania zdarzenia asynchronicznego musisz stworzyć obiektu typu **Future**. Pora na większą ilość praktyki, a co za tym idzie kodu!

> W świecie Fluttera mówi się, że wszystko jest widgetem. Teraz już wiesz że to kłamstwo, bo wszystko jest tak na prawdę eventem.

# Future

W celu zaplanowania i uruchomienia dowolnego kodu na kolejce asynchronicznej tworzymy nowy obiekt typu **Future**. Jeśli pisałeś wcześniej w JavaScript to sposób działania jest identyczny jak w przypadku obiektu *Promise*. A jeśli nie pisałeś? Spójrzmy:

```dart
import "dart:async";

void main() {
  print("start main");
  Future(() => print("future"));
  scheduleMicrotask(() => print("micro"));
  print("end main");
}
```

```
1. start main
2. end main
3. micro
4. future
```

Printy **1** oraz **2** to kod uruchamiany synchronicznie. *micro* wskakuje jako **3**, ponieważ kolejka *microtask* ma pierwszeństwo przed jakimkolwiek zadaniem na kolejce *event*. Ostatni jest sam *future*.

Spójrzmy jeszcze na przykład z większą liczbą zdarzeń:

```dart
import "dart:async";

void main() {
  print("start main");
  Future(() => print("future1"));
  Future(() => print("future2"));
  Future(() => print("future3"));
  print("end main");
}
```

```
1. start main
2. end main
3. future1
4. future2
5. future3
```

Nie ma tutaj żadnego zaskoczenia, a jedynie potwierdzenie faktu że zdarzenia są dodawane do kolejki i uruchamiane w odpowiedniej kolejności *future1 -> future2 -> future3*. Wcześniej dodane zdarzenie oznacza szybsze jego podjęcie i wykonanie.

Warto również przypomnieć raz jeszcze fakt jednowątkowości. To, że zaplanowaliśmy 3 zdarzenia nie oznacza wcale że będą one wykonywane równolegle (**parallel execution**), tylko asynchronicznie (**asynchronous execution**). Jedno po drugim, ze złudzeniem że wszystko to odbywa się w jednym czasie. Iluzja wielowątkowości.

## Future.delayed

*Future* udostępnia pomocny konstruktor `Future.delayed` do zaplanowania zdarzenia, które zostanie wykonane po zadanym czasie. Zdarza mi się nie raz korzystać z tego zapisu, aby zasymulować opóźnienie jak np. przy pobieraniu danych z sieci. Samo działanie jest stosunkowo proste. Pod spodem rejestrowany jest licznik, który dokładnie po upływie zadanego okresu wrzuci do *Event Loopa* nowe zadanie do wykonania.

Spójrz na poniższy przykład. Funkcje *randomSort* i *listsAreEqual* śmiało ignoruj, bo są tutaj tylko po to, aby wprowadzić wolny kod o losowej złożoności.

```dart
void main() {
  Future(() => print("future1"));
  Future.delayed(
    Duration(milliseconds: 500),
    () => print("future delayed"),
  );
  randomSort([2,1,3,4,5,6,7,8,9,10]);
  Future(() => print("future2"));
}


List<int> randomSort(List<int> numbers) {
  final easy = [...numbers]..sort();
  final sorted = [...numbers]..shuffle();
  
  while (!listsAreEqual(easy, sorted)) {
    sorted.shuffle();
  }
  
  return sorted;
}

bool listsAreEqual(l1, l2) {
  return List.generate(
    l1.length, (i) => l1[i] == l2[i]
  ).where((r) => r)
  .toList().length == l1.length;
}
```

Jaka będzie kolejność wykonania?

**A.**
```
1. future1
2. future delayed
3. future2
```

**B.**
```
1. future1
2. future2
3. future delayed
```

Wszystko zależy od tego ile akurat zajmie posortowanie listy. Więcej niż 500ms? **A**. Mniej? **B**.

## Future i jego API

Powyższe przykłady wyglądają tak, jakby jedyna różnica w udostępnionym interfesie między *microtask*, a *event* polegała na słownictwie. W pierwszym mamy wywołanie funkcji `scheduleMicrotask`, a w drugim zawołanie `Future`. Z tym, że ten `Future()` to konstruktor, który utworzy nowy obiekt zdarzenia i zwróci nam go abyśmy mogli na nim dalej pracować i nim zarządzać. W przypadku *microtask* nie mamy takiego komfortu, gdyż zwraca nam on nic, czyli *void*.

Świeżo utworzony *Future* znajduje się w stanie **zarejestrowanym**, ale nie wykonanym. *Event Loop* wie już o jego istnieniu i wrzucił go na kolejkę *Event Queue*, ale w żadnym wypadku nie wykonuje zawartych w nim instrukcji. Na to przyjdzie pora w przyszłości (*future*) i kiedy ona nadejdzie stan automatycznie przejdzie w **wykonany**.

### Łańcuch wywołań (then)

Obiekt typu *Future* udostępnia funkcję **then**, która zostanie zawołana niezwłoczenie po tym, gdy wszystkie zawarte w nim instrukcje zostaną poprawnie wykonane. Taki sygnał zwrotny (**callback**) do poinformowania aplikacji, że wszystko poszło gładko i zwrócenia ostatecznego wyniku operacji.

```dart
import "dart:async";

void main() {
  print("start main");
  
  Future(() {
    print("future1");
    return "future1 completed";
  }).then((msg) => print(msg));
  Future(() => print("future2"));

  print("end main");
}
```

```
1. start main
2. end main
3. future1
4. future1 completed
5. future2
```

Warto zapamiętać, że *then* jest uruchamiany w sposób synchroniczny, zaraz po tym jak wykona się ostatnia zaplanowana instrukcja wewnątrz aktualne przetwarzanego zdarzenia. To dlatego *future1 completed* wywoła się przed *future2*.

Ale super jest ten *then*! Tylko po co go używać skoro równie dobrze można wszystko zapisać następująco:

```dart
import "dart:async";

void main() {
  print("start main");

  Future(() {
    print("future1");
    print("future1 completed");
  });
  Future(() => print("future2"));

  print("end main");
}
```

Wynik identyczny, więc o co chodzi? O szczegół implementacyjny. Wyobraź sobie teraz, że nie piszesz własnego *future* tylko używasz wbudowanego w bibliotekę, powiedzmy do wysłania zapytania do API. Przykład banalny, ale świetnie obrazuje kiedy używany jest *then* i że pełni on kluczową rolę w przetwarzaniu asynchronicznym.

```dart
import "dart:async";

void main() {
  http.get("http://moje_api/users")
    .then((users) => print(users));
}
```

Wysyłamy zapytanie na adres *http://moje_api/users* i jak tylko pojawi się odpowiedź (może minąć nawet kilka sekund) pokazujemy ją na konsoli. Tadam, po to jest *then*.

Wszystko jest piękne tak długo jak nie ma żadnych błędów, a dobrze wiemy że takie aplikacje nie istnieją. Błąd zawsze przyjdzie i wskoczy znienacka na użytkownika w najmniej testowanym scenariuszu. Nie sprawdziłeś jak aplikacja się zachowa przy wyciąganiu danych z API, gdy nie ma internetu? A no tak, przy developmencie przecież zawsze jesteś wpięty w firmowe wi-fi :) 

### Obsługa błędów (catchError)

A skoro już o błędach mówimy to *Future* jest o tyle specyficznym konstruktem, że łapaniem jego wyjątków nie zajmuje się uniwersalny blok *try-catch*.

Jaki będzie wynik uruchomienia takiego programu?

```dart
import "dart:async";

void main() {
  print("start main");

  try {
    Future(() {
      int.parse("future");
    });
  } catch (_) {
    print("ooops");
  }

  print("end main");
}
```

```
1. start main
2. end main
Unhandled exception:
FormatException: ...
```

Delikatnie zaskakujące, ale *try-catch* nie obsłużył wyjątku i zamiast ładnego *ooops* aplikacja się crashuje. Bardzo niedobrze, ale na logikę to uzasadnione działanie. Blok złapie jedynie wyjątek tworzenia samego obiektu *Future*, a nie jego funkcji która uruchomi się gdzieś-kiedyś, a na pewno nie w aktualnym przebiegu *Event Loopa*. Jest to duża pułapka o której programista często zapomina, co udowadnia np. kod z oficjalnej paczki do obsługi *Cloud Functions* (*Firebase*):

[![Async catch issue in Flutterfire](/assets/img/blog/async/flutterfire_async_catch.png)](https://github.com/FirebaseExtended/flutterfire/blob/master/
packages/cloud_functions/cloud_functions/lib/src/https_callable.dart#L30)

Jest *try-catch*? Jest. Jest *Future*? Jest, co sugeruje użycie *then*. Jest złapany wyjątek w razie problemów? Nie ma. Zdarza się jak widać nawet najlepszym, ale jak w takim razie obsłużyć błędy we właściwy sposób?

```dart
import "dart:async";

void main() {
  print("start main");

  Future(() {
    int.parse("future");
  }).catchError((_) {
    print("ooops");
  });

  print("end main");
}
```

```
1. start main
2. end main
3. ooops
```

Z użyciem funkcji **catchError**! Analogicznie jak *then* uruchamiany jest w momencie gdy wszystko przebiegło poprawnie, tak *catchError* zostanie wywołany jeśli funkcja rzuciła w dowolnym momencie wyjątkiem. Taki *try-catch* w wersji funkcyjnej.

# Test!

Czas na kartkówkę i skondensowanie całej wiedzy w jednym przykładzie. Pochodzi on ze świetnego [artykułu](https://web.archive.org/web/20170704074724/https://webdev.dartlang.org/articles/performance/event-loop) na temat asynchroniczności w Darcie, który serdecznie polecam. To jak, gotowy? Skompiluj w głowie poniższy kod i ustal poprawną kolejność wykonania. Zaliczenie tak złożonego przykładu udowadnia że kolejność uruchamiania kodu nie stanowi dla ciebie już żadnego wyzwania. Poważnie, nie ma już niczego więcej!

```dart
import "dart:async";

void main() {
  print('start main');
  scheduleMicrotask(() => print('micro1'));

  Future.delayed(
    Duration(seconds: 1),
    () => print('future1'),
  );

  Future(() {
    print('future2');
  }).then((_) {
    print('future2.1');
  }).then((_) {
    print('future2.2');
    scheduleMicrotask(() => print('future2.micro1'));
  }).then((_) => print('future2.3'));

  scheduleMicrotask(() => print('micro2'));

  Future(() {
    print('future3');
  }).then((_) {
    Future(() => print('future3.1'));
  }).then((_) {
    print('future3.2');
  });

  Future(() => print('future4'));
  scheduleMicrotask(() => print('micro3'));
  print('end main');
}
```

Gotowy?

Oto prawidłowa odpowiedź:

```
1. start main
2. end main
3. micro1
4. micro2
5. micro3
6. future2
7. future2.1
8. future2.2
9. future2.3
10. future2.micro1
11. future3
12. future3.2
13. future4
14. future3.1
15. future1
```

Jak do tego doszło? Główne zasady dla przypomnienia:
1. Kod synchroniczny nie może zostać przerwany.
2. Gdy Event Loop dostaje sterowanie to najpierw uruchamia wszystkie microtaski na kolejce.
3. Future muszą czekać aż kolejka microtasków się opróżni.
4. Wywołanie *then* działa w sposób synchroniczny, Event Loop nie dostaje jeszcze możliwości dalszego sterowania.
5. `Future.delayed` jest opóźniony.

# async / await

Standardowa składnia do pracy z kodem asynchronicznym ma dwie zasadnicze wady:

1. Wspomniana już **obsługa wyjątków** w której musimy pamiętać z jakiego mechanizmu skorzystać. Blok *try-catch* dla kody synchronicznego i *catchError* dla asynchronicznego. Jest to rozwiązanie podatne na błędy programisty.
2. **Trudny w śledzeniu kod**. Chcąc prześledzić kolejność wykonania funkcji należy się dodatkowo skupić na wszystkich obiektach *future* i ich własnym łańcuchu wywołań *then*.

A co gdyby istniał mechanizm, który rozwiązuje oba te problemy? Możliwość czytania dowolnego kodu od góry do dołu, bez znaczenia czy jest synchroniczny, czy asynchroniczny. Łapania błędów w ustandaryzowany sposób nawet gdy jest uruchamiany przez *Event Loop*. To nie mrzonka, a najprawdziwszy fakt. Dart dostarcza programiście dwa krótkie słowa kluczowe, które sprawiają, że kod zawsze wygląda jakby był przetwarzany synchronicznie.

Ta dodatkowa składnia to **async** i **await**. Nie dają one co prawda nowych możliwości w kontekście funkcjonalnym, a jedynie wprowadzają cukier składniowy (**syntatic sugar**), aby kod był czytelniejszy i bardziej utrzymywalny.

## async

Słowo kluczowe **async** służy do oznaczenia funkcji jako asynchronicznej. Użycie go niesie za sobą następujące implikacje:

1. Typ zwracany przez funkcję automatycznie staje się *Future\<T>*. Nawet w najprostszym scenariuszu jak ten:

```dart
int getOne() async => 1;
```

Uruchomienie takiej funkcji zakończy się błędem, bo zwrócony typ to nie *int*, a *Future\<int>*. Poprawny kod:

```dart
Future<int> getOne() async => 1;
```

2. W obrębie funkcji dozwolne jest używanie słowa kluczowego **await**. Bez *async* nie ma *await*. Jak *yin* i *yang*.

## await

Zadaklarowałeś funkcję jako asynchroniczną i co dalej? Masz teraz pełne prawo do potraktowania dowolnego obiektu *future* składnią **await**. W gruncie rzeczy jest to taki *then* na sterydach.

```dart
import 'dart:async';

Future<void> main() async {
  print("1");

  try {
    await Future(() {
      print("2");
      int.parse("future");
    });
  } catch (_) {
    print("3");
  }

  print("4");
}
```

```
1
2
3
4
```

Kod czytany od góry do dołu, łapiący asynchroniczny wyjątek i nie wymagający żadnego skupienia żeby poprawnie wskazać jaka będzie kolejność printowania. O to właśnie chodziło. *await* nakazuje aby poczekać na pełne wykonanie kodu asynchronicznego i dopiero gdy się on zakończy przejdzie dalej.

Nie jest to jednak jakby się mogło wydawać instrukcja blokująca nasz jedyny wątek. Napotykając na *await* następuje zwolnienie wykonania i przekazanie kontroli do *Event Loop*. Analogicznie jak dla *then*, bo w praktyce on tutaj jest, a jedynie ukrywa się za ładniejszą składnią.

# Zachłanny microtask

Bardziej jako ciekawostka, niż coś co przydaje się w codziennym pisaniu aplikacji jest zachłanność kolejek. A raczej kolejki, która ma wyższy priorytet - *Microtask Queue*.

```dart
import 'dart:async';

void attentionPlease() {
  scheduleMicrotask(() => attentionPlease());
}

void main() {
  print("start main");
  Future(() => print("future is now"));

  scheduleMicrotask(() => print("micro1"));
  attentionPlease();
  scheduleMicrotask(() => print("micro2"));

  print("end main");
}
```

```
start main
end main
micro1
micro2
???
```

Gdzie się podział *future is now*? Czeka na swoją kolej aż wyższa kolejka się opróżni. Nie nastąpi to jednak nigdy bo gdy tylko kończy się jedno zadanie, dodawane jest kolejne i tak w nieskończoność.

1. Wypisz **start main**
2. Dodaj *future* do kolejki
3. Dodaj *microtask* **micro1** do kolejki
4. Uruchom funkcję która doda nowy *microtask* do kolejki
5. Dodaj *microtask* **micro2** do kolejki
6. Wypisz **end main**
7. Uruchom microtask: **micro1**
8. Uruchom microtask, który dodaje do kolejki następny microtask
9. Uruchom microtask: **micro2**
10. Uruchom microtask, który dodaje do kolejki następny microtask
11. Uruchom microtask, który dodaje do kolejki następny microtask
12. Uruchom microtask, który dodaje do kolejki następny microtask

... i tak w nieskończoność dopóki ręcznie nie zamkniemy programu. *Future* najzwyczajniej w świecie nie ma szansy zostać podjętym przez *Event Loop*. Zapomniany na wieki. Problem można łatwo rozwiązać poprzez ... nieużywanie `scheduleMicrotask` jeśli nie jest to całkowicie niezbędne.

# Podsumowanie

Dart pomimo tego, że jest jednowątkowy to radzi sobie świetnie z przetwarzaniem instrukcji i zachowaniem responsywności swojego jedynego wątku. Zadania nie wymagające czasu pracy procesora jak ruch sieciowy, czy operacje na plikach wykonują się z boku i tylko w odpowiednim momencie dorzucają nowe zdarzenia do *Event Loop*. Kieruje on i decyduje o tym co ma się teraz wykonać z uwzględnieniem dwóch podległych kolejek. Pisz kod z wykorzystaniem *async/await*, aby ograniczyć ryzyko błędów i ułatwić sobie powrót do takiego kodu za jakiś czas. To na prawdę działa.

Jest to pierwszy wpis z serii dotyczącej przetwarzanie instrukcji w aplikacjach Flutterowych. W najbliższym czasie spodziewaj się kontynuacji, która zachwieje faktem wykonywania tylko jednej instrukcji w danym czasie, nawet pomimo wspomnianej już kika razy jednowątkowości. Do następnego!
