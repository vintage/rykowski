---
color: rgb(69,190,247)
title: Schludny i czysty kod
date: "2020-08-04"
layout: "post"
slug: "flutter-linter"
excerpt: "Trzymanie się restrykcyjnych zasad zapewniających spójność kodu to podstawa udanego projektu. Dowiedz się jak skonfigurować lintera, aby trzymał wszystko w ryzach."
thumbnail: "assets/img/blog/linter/thumbnail.png"
---

Prawdopodobnie często spotykasz się z publicznie udostępnianym kodem. Biblioteki open-source, gotowe snippety, czy kod prezentowany w formie zrzutów ekranu. To może być nawet kod prywatny, wewnątrzzespołowy, gdzie wkład ma więcej niż jeden programista. Można go (kod, nie programistę) najprościej skategoryzować jako **ładny** oraz **brzydki**.

**Ładny kod** jest po prostu ładny. Napisany zgodnie z wytyczonymi standardami i konwencjami języka, dobrze sformatowany, trzymający się spójnej formy. Nie budzi negatywnego pierwszego wrażenia. **Brzydki kod** natomiast czuć z daleka. Puste linie niewiadomego pochodzenia, dziwne wcięcia, nieużywane importy, polskie nazwy zmiennych i dziesiątki innych rzeczy od których metaforycznie krwawią oczy miłośnikom estetyki.

Jak sprawić zatem, aby brzydki kod stał się ładny, albo chociaż względnie przyzwoity na dobry początek? Narzędziowo.

## dartfmt

Najłatwiejszą i najmniej czasochłonną rzeczą jaką możesz zrobić dla swojego projektu jest korzystanie z narzędzia do automatycznego formatowania kodu. Wskazujesz w nim jedynie jakie pliki powinny zostać doprowadzone do ładu, a skrypt zajmie się całą resztą. Weźmy dla przykładu kod:

```dart
class User {
  String name; User(this.name);
}

void main() {
  final user =     new User('Kamil');


  print(user.name)  ;



  if ( 1  +1==2) { print("Super!");}
}
```

Dużo pustych linii, spacji raz za dużo, raz za mało, zbędne słowo kluczowe *new*. Dość groteskowo, ale zbliżone jakościowo patalogie można trafić również w prawdziwym świecie. Jak temu zaradzić? Wystarczy uruchomić *dartfmt*!

```sh
dartfmt my_file.dart --fix -w
```

Wynikiem jest kod przeformatowany na który można już patrzeć bez zbędnego obrzydzenia.

```dart
class User {
  String name;
  User(this.name);
}

void main() {
  final user = User('Kamil');

  print(user.name);

  if (1 + 1 == 2) {
    print("Super!");
  }
}
```

Flaga `--fix` określa, że chcemy sformatować plik wszystkimi wspieranymi regułami. Bez niej formatowane są jedynie znaki niedrukowane (*whitespace characters*), czyli spacje, nowe linie i tym podobne. `-w` nanosi sformatowane zmiany bezpośrednio na pliku, zamiast pokazywać je wyłącznie na konsoli. Wygodne.

A wiesz co jest jeszcze wygodniejsze? Formatowanie bezpośrednio z *IDE*, niezależnie od tego czy korzystasz z *Android Studio*, czy *Visual Studio Code*. Zawsze masz pod ręką opcję *Reformat Code with dartfmt* / *Format Document* dostępną w menu kontekstowym (prawy przycisk myszy). Przy takiej prostocie grzechem jest wrzucanie wersji nieobrobionej w świat. Pamiętaj o tym i formatuj na potęgę. Zwłaszcza, gdy ktoś poza tobą ma szanse spojrzeć do kodu.

Formatowanie kodu to podstawa, ale duże projekty potrzebują czegoś znacznie większego. Listy reguł jasno definiujących co jest dozwolone, a co nie. W jaki sposób pisać kod aplikacji, aby wszędzie był spójny i trzymał się utartego szlaku. Jest to szczególnie ważne w projektach wieloosowobych, aby "spłaszczyć" różnice w kodzie pisanym przez każdego z programistów. Nie oszukujmy się, każdy ma swój własny styl pisania. Dla projektu lepiej jednak będzie, gdy nie jesteś w stanie na podstawie kilku linii kodu określić kto dokładnie je wyrzeźbił.

## linter

Uzupełnieniem dla formatowania kodu, są poprawnie skonfigurowane reguły dla **lintera**, czyli narzędzia analizującego kod aplikacji pod kątem błędów stylistycznych, czy niepoprawnych konstrukcji. Programista (zespół) określa wytyczne których chce się trzymać, aby projekt był spójny, a kod przyjemny w utrzymywaniu. Narzędzie dopilnuje, aby reguły te były przestrzegane przez wszystkich uczestników.

### Przygotowanie

Konfigurację *lintera* zacznę od tego, że nie będzie to projekt Flutterowy, a jedynie Dartowy. Wszystkie przedstawione zasady przenoszą się oczywiście 1:1, ale nie chcę zaciemniać obrazu analizy dodatkową składową w postaci frameworka.

W nowym katalogu *dart_side* tworzymy plik *my_file.dart*. Plik sam w sobie został już sformatowany i *dartfmt* nie może nic więcej dla nas zrobić.

```dart
class user {}

main() {
  String me = null;

  if (me == 'Kamil') {
    print("Hi");
  }
}
```

### Uruchomienie

Sprawdźmy co *linter* myśli o powyższym pliku. Mam do niego trochę osobistych obiekcji, ale zdajmy się na fachowe narzędzie, zamiast na omylne oko programisty.

```sh
dartanalyzer .
```

Wynik?

```
Analyzing linter...
No issues found!
```

 *Linter lubi to*. W gruncie rzeczy to co ma mu się nie podobać? Kod się kompiluje, działa. Niczego więcej do szczęścia nie potrzeba. A jednak ...

### Ustalenie reguł

Domyślnie *linter* jest łagodny i wszystko traktuje zbyt optymistycznie. Nie ma reguł na dobro i zło. Czego byś mu nie dał do sprawdzenia i tak dostaniesz najwyższą notę. To tak jakbyś oddał dyktanto do sprawdzenia przez dysortografa - przeczyta, a na koniec powie, że jemu to się generalnie wszystko podoba.

Sterowanie regułami dotyczącymi kodu odbywa się poprzez plik konfiguracyjny **analysis_options.yaml** umieszczany w głównym katalogu projektu, na tym samym poziomie co np. *pubspec.yaml*. Definiujemy w nim listę zasad których chcemy trzymać się w projekcie, takich jak:
- podwójne, czy pojedyncze apostrofy dla stringów
- nie inicjalizowanie zmiennych *nullem*
- nie korzystanie z funkcji *print*
- ... 

Samych wytycznych, czyli potencjalnych reguł do podążania jest całkiem sporo, a wszystkie wraz z opisem znajdziesz na [Linter for Dart](https://dart-lang.github.io/linter/lints/). My użyjemy tylko kilku, żeby zobaczyć jak to ugryźć. Dodawanie kolejnych nie stanowi już żadnego problemu i odbywa się w analogiczny sposób.

W pliku *analysis_options.yaml* zdefiniujemy cztery reguły dla *lintera*, aby był w stanie stwierdzić czego w kodzie należy unikać:

```yaml
linter:
  rules:
    - prefer_double_quotes
    - avoid_init_to_null
    - avoid_print
    - camel_case_types
```

Ponowne uruchomienia analizy na pliku wskaże już realne problemy, które należy zaadresować. Przedstawiona jest informacja o tym jaka reguła została naruszona oraz w którym dokładnie miejscu w aplikacji należy ją poprawić.

```
Analyzing linter...
  lint • Name types using UpperCamelCase. • my_file.dart:1:7 • camel_case_types
  lint • Don't explicitly initialize variables to null. • my_file.dart:4:10 • avoid_init_to_null
  lint • Prefer double quotes where they won't require escape sequences. • my_file.dart:6:13 • prefer_double_quotes
  lint • Avoid `print` calls in production code. • my_file.dart:7:5 • avoid_print
4 lints found.
```

### Gotowy szablon

Czy przeglądanie wszystkich dostępnych reguł i podejmowanie decyzji o ich słuszności to coś co chciałbyś robić? Ja na pewno nie. Jest dużo ciekawszych rzeczy na ktore mam ochotę i chęci, więc to zadanie najlepiej jest oddelegować. Z pomocą przychodzą gotowe biblioteki, których jedynym zadaniem jest dostarczenie predefiniowanego zestawu reguł, aby kod był po pierwsze schludny, a po drugie trzymał się reguł uznawanych przez inne projekty w ekosystemie Darta, czy Fluttera.

Użycie gotowego szablonu sprowadza się do dwóch rzeczy. Pierwszą jest dodanie zależności w *pubspec.yaml*, a drugą użycie wzorca w *analysis_options.yaml*. Dwie najpopularniejsze paczki to [pedantic](https://pub.dev/packages/pedantic) i [lint](https://pub.dev/packages/lint/score), gdzie ja jestem zwolennikiem tej drugiej. Jest bardziej restrykcyjna (wymusza więcej reguł), a tego z reguły oczekujemy korzystając z *lintera*.

```yaml
# pubspec.yaml
dev_dependencies:
  lint: ^1.2.0
```

```yaml
# analysis_options.yaml
include: package:lint/analysis_options.yaml
```

Nie ma tutaj żadnej magii, a jedyne co się dzieje to zaimportowanie reguł z [analysis_options.yaml](https://github.com/passsy/dart-lint/blob/master/lib/analysis_options.yaml).

Jeśli nie podoba ci się jakakolwiek reguła użyta w szablonie, lub masz własne preferencje co do niektórych z nich, możesz je bez problemu dostosować do własnych potrzeb. Też to robię, bo zawsze zostaje kilka reguł co do których mam odmienne zdanie niż *lint*, czy *pedantic*. W takim wypadku wystarczy nadpisać jedynie reguły które chcemy dostosować pod własne potrzeby.

```yaml
include: package:lint/analysis_options.yaml

linter:
  rules:
    public_member_api_docs: false
    lines_longer_than_80_chars: false
    sort_constructors_first: true
```

### Wyjątki od reguły

Jak mówi polskie powiedzenie *"Wyjątek potwierdza regułę"* i ta sama zasada dotyczy reguł *lintera*. Pomimo posiadania zestawu restrkcyjnych reguł dla projektu, mamy możliwość wyłączania pojedynczych z nich dla konkretnej linii, bądź nawet całego pliku. Po co to jednak robić? Przecież nie po to wdrażamy *lintera*, żeby teraz ignorować jego sugestie. Otóż to, nie chcemy ich ignorować, ale czasami po prostu musimy.

Weźmy taką regułę jak *avoid_print*, która nie pozwala wstawiać *printów* w kodzie. Ma sens, przecież nikt nie lubi śmietnika w kodzie i spamu w konsoli uruchomieniowej. Tylko co zrobić, gdy na prawdę w kilku miejscach chcemy takiego printa wstawić? Chociażby po to, żeby wypisać token użytkownika po zalogowaniu, w celu wykorzystania go poza aplikacją podczas developmentu.

Plik w którym łamiesz zasadę lintera (rób to w pełni świadomie!) wystarczy opisać komentarzem na samej jego górze:

```dart
// ignore_for_file: avoid_print
```

Szczerze to nigdy nie korzystam z tego dobrodziejstwa. Kończy się na tym, że regułę musiałem złamać wyjątkowo raz, a *linter* przestaje jej już pilnować dla całego pliku. Z czasem reguła ta będzie omyłkowo łamana coraz częściej, co doprowadzi do niepotrzebnych zgrzytów.

Drugi sposób jest bardziej subtelny i mniej inwazyjny. Również polega na użyciu komentarza, ale jego zasięg to nie cały plik, a jedynie pojedyncza linia. Zapobiega to nadużyciom i jasno widać gdzie i dlaczego reguła musiała zostać pominięta.

```dart
// ignore: avoid_print
print("Need to print, sorry");
```

### Wsparcie IDE

Trudno oczekiwać od programisty, żeby w prawdziwym świecie co chwila przechodził do terminala i uruchamiał lintera w poszukiwaniu błędów. Komendy konsolowe są oczywiście przydatne, bo można je uruchamiać na poziomie *CI/CD* podczas automatycznego budowania, ale jako człowiek mamy coś lepszego.

Wsparcie z poziomu *IDE*, które również rozpoznaje zdefiniowane zasady w *analysis_optioms.yaml*. Potrafi w trybie graficznym wskazać problemy w czasie rzeczywistym. Żółte podkreślenia, czerwone, żarówki, w zależności od tego jak jest to zdefiniowane w twoim edytorze, takie wskazówki otrzymasz w codziennej pracy. Jest to pierwsza linia rozwiązywania problemów, bo zawsze widoczna - problemy pojawiają się tylko wtedy gdy dodajesz, lub usuwasz kod, więc i tak jesteś w danym pliku i masz szansę od razu zareagować na błędy.

## Flutter way

Pracując nad aplikacją Flutterową nie musisz pamiętać o polecaniach *dartfmt*, czy *dartanalyzer*. Są one odpowiednio opakowane w:

```dart
// Format all files in current directory
flutter format .
```

```dart
flutter analyze
```

Jest to delikatnie wygodniejsze, bo w razie problemów z pamięcią wystarczy uruchomić `flutter --help` i odnaleźć zapomnianą komendę.

## Podsumowanie

Dbanie o jakość kodu jest procesem żłożonym. Wpływa na niego wiele czynników, gdzie jednym z nich jest kwestia stylistyczna i zachowanie spójności w obrębie projektu. Warto poświęcić chwilę czasu na początku, aby określić czego oczekujemy i trzymać się zdefiniowanych zasad. Ułatwia to dalszy rozwój i utrzymanie kodu, a także najzwyczajniej w świecie programista ma łatwiejsze życie. Nie myśli przy pisaniu o tym jak sformatować kod - problem ten spada na narzędzie. Nawet sam proces *code review* staje się mniej stresujący, bo nikt nie będzie wspominał o tym, że wolałby jednak tu widzieć podwójne, a nie pojedyncze apostrofy.