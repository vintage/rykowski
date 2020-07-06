---
title: Nowy gracz aplikacji mobilnych - Flutter
date: "2019-06-09"
layout: "post"
slug: "flutter-intro"
excerpt: "Każda korporacja chce ukroić kawałek z tortu aplikacji x-platform. Microsoft kupił Xamarina, Facebook stworzył React Native, a teraz Google dołącza do wyścigu z Flutterem - nowoczesnym frameworkiem do pisania aplikacji Android oraz iOS."
---

**Flutter** to nowa technologia od Google służąca do tworzenia aplikacji mobilnych. Nie ma tutaj jednak mowy o stricte natywnym podejściu (od tego jest Kotlin, czy Swift), lecz o cross-platformowym frameworku, w którym kod piszemy raz i robimy release na oba systemy - Android oraz iOS. Brzmi świetnie, prawda? I tak jest w rzeczywistości. Nie utrzymujemy dwóch całkowicie niezależnych bytów natywnych, nie uczymy się dwóch odmiennych od siebie języków programowania, nie poznajemy tak dogłębnie dwóch różnych systemów, czy też środowisk. W naszej kwestii jako programisty pozostaje wytworzenie aplikacji mobilnej, a Flutter zajmie się tym, aby poprawnie działała na obu platformach. 

![meme_follow.gif](/assets/img/blog/meme_follow.gif)

## Deja-vu

Czy Flutter to rewolucja na miarę naszych czasów? Całkowity zwrot świata mobilno-developerskiego w stronę aplikacji którą piszemy raz i uruchamiamy gdziekolwiek chcemy? Bez przesady - rozwiązania tego typu istnieją już od jakiegoś czasu, a mimo to development natywny ma się bardzo dobrze i nigdzie się nie wybiera. Fluttera należy traktować zdecydowanie bardziej jako ewolucję w sposobie w jakim piszemy aplikacje tego typu. 

Na rynku mamy co najmniej kilkanaście innych rozwiązań, które celują w ten sam problem - jak ~~zarobić~~ zbudować aplikację mobilną która pokryje oba systemy i się nie narobić. Każda technologia rozwiązuje problem na inny sposób, z lepszym bądź gorszym skutkiem, który ostatecznie definiuje jak bardzo przyjmie się na rynku i jak dużą świadomość wywrze na developerach. Oto lista narzędzi z którymi miałem przyjemność mniej-lub-bardziej pracować:

### Ionic

Najstarsze z wymienianych tutaj rozwiązań i w moim odczuciu najsłabsze. Pomimo tego że projekt jest aktywnie rozwijany i dostarcza naprawdę fajny ekosystem, to czasy swojej świetności ma już dawno za sobą. Aplikacje pisane są w *JavaScript*, *HTML* i *CSS*. Tak, jest to bardzo znajomy stack technologiczny dla web-developerów, ale nie bez powodu. Aplikacje napisane w Ionicu nie są kompilowane do niczego co choćby przypominałoby kod natywny. Sztuczka budowania polega na tym, że tworzymy naszą aplikację (zupełnie jak stronę internetową), a następnie jest ona pakowana w taki sposób, aby jej uruchomienie na fizycznym urządzeniu odpaliło w tle przeglądarkę, która obsłuży w trybie pełnoekranowym naszą aplikację. Rozwiązanie jest mało wydajne, zwłaszcza gdy operujemy na dużej ilości danych, lub gdy chcemy wprowadzić zaawansowane animacje do projektu. Nie jest jednak tak, że Ionic jest złą opcja gdy wybieramy główne narzędzia do stworzenia aplikacji mobilnej. W przypadku, gdy zależy nam na:

- stworzeniu produktu który będzie działał z pudełka na Android, iOS oraz przeglądarce
- szybkim prototypie aby zwalidować pomysł biznesowy
- błyskawicznym wykorzystaniu znanych technologii (JS, HTML, CSS)

Warto poważnie rozważyć tę technologię jako dobrego kandydata. Możemy szybko przygotować prototyp, który będzie dostępny dla użytkowników mobilnych (aplikacja pobierana ze sklepu), jak również przez przeglądarkę dla użytkowników desktopowych, czy tych którzy korzystają z innego systemu mobilnego (m.in. Windows Phone).

### Xamarin

Prawdziwka gratka dla programistów platformy .NET i języka C#, którzy chcą stworzyć aplikację mobilną. Właścicielem frameworka jest Microsoft, który w 2016 roku przejął startup i od tego momentu kieruje jego dalszym rozwojem. Jest to jedyna technologia z którą nie mam prawdziwego komercyjnego doświadczenia - jedynie projekty hobbystyczne - głównie za sprawą faktu, że nie pojawiła się między nami chemia (przez "nas" mam na myśli C# i mnie). Po prostu, tak bywa. Warto zaznaczyć, że w przeciwieństwie do Ionica mamy tutaj do czynienia z aplikacją, która kompiluje się do natywnego (w uproszczeniu) kodu. Daje nam to spory skok wydajnościowy, dzięki któremu użytkownicy aplikacji nie są w stanie stwierdzić na podstawie płynności działania, czy korzystają z aplikacją natywnej, czy też nie. A do tego przede wszystkim sprowadza się pisanie oprogramowania typu cross-platform.

### React Native

Z jednej strony mój ex-ulubieniec, z drugiej koszmarek który śni się po nocach. Głównym motorem napędowym jest jak w przypadku Ionica - JavaScript. Można ten język kochać, można nienawidzić, a można (to ja!) po prostu traktować go jako kolejne narzędzie wykorzystywane w pracy, które jak wszystko - ma swoje wady i zalety - wystarczy je poznać i zaakceptować. Nie chcę się jednak rozwodzić nad językiem, a nad frameworkiem. Jest to na dzień dzisiejszy czarny koń wyścigu ~~zbrojeń~~ cross-platformowego. Najpopularniejsze dłuto do rzeźbienia naszych cross-platformowych aplikacji. Posiada ogromny ekosystem (paczki które zrobią za nas 50% projektu) oraz community (stackoverflow, eventy, grupy dyskusyjne). Jeśli mowa o paczkach - te są naprawdę w dużej części dobre. Co to są dobre paczki? A no takie, które są dojrzałe, utrzymywane, aby działały na kolejnych wersjach Androida, czy iOSa, a także wystawiają bogaty interfejs do operowania. Prawdziwa machina open-source. Dodatkowo bardzo często klienci, którzy potrzebują aplikacji mobilnej chcą aby została napisana właśnie w React Native. Skąd klient stricte biznesowy wie o technologii? Dlaczego akurat ta, a nie inna? To proste - marketing, rozpoznawalność, dojrzałość na rynku sprawiły, że gdy myślisz o aplikacji mobilnej ze współdzielonym kodem to mózg automagicznie rysuje wizerunek *React Native*. 

Czy mamy więc rozwiązanie idealne? Oczywiście, że nie - nie ma rozwiązań idealnych, a każde kłuje na swój własny specyficzny sposób. Z projektów nad którymi pracowałem w tej technologii wyłania się zawsze podobny zestaw problemów:

- trudność w aktualizacji w istniejącym projekcie - podbicie wersji React Native niemal zawsze prowadzi do traumy i rozwiązywania mistycznych błędów, które nie powinny mieć miejsca. W skrajnych przypadkach najlepszym wyjściem z sytuacji jest utworzenie czystego projektu z nową wersją React Native i przenoszenie tam naszego kodu projektowego - brzmi źle, ale skoro działa to warto się pochylić
- niedeterministyczne działanie aplikacji - nie zliczę sytuacji w których aplikacja budowała się poprawnie w piątek, a w poniedziałek po południu coś było nie tak. A to przestał działać live reload, a to hot reload dostaje czkawki. Aplikacja przestała się budować, lub urządzenie nie widzi bundla developerskiego? Standard. W pewnym momencie po prostu się przyzwyczajasz i masz swój własny zestaw komend defibrylacyjnych, którymi się dzielisz z zespołem projektowym.
- jakość zgłaszanych błędów - czerwony ekran który jako developerzy dostajemy co jakiś czas, gdy zepsujemy nasz kod. O ile doskonale rozumiem, że błędy się zdarzają i nikt nie pisze kodu w taki sposób, aby nie był mu potrzebny backspace, to komunikaty które dostajemy wołają o pomstę do debuggera. Tak jak w punkcie wyżej - po jakimś czasie mózg buduje mapę co znaczy dany komunikat i w jakich przypadkach się pojawia (jak choćby błędny import) i z automatu wiemy co nabroiliśmy

Są również inne mniejsze, bądź większe problemy, jednak bardziej specyficzne dla konkretnego projektu. Mimo wad, szczerze uważam, że React Native jest technologią w którą warto inwestować swój czas - m.in. za sprawą mnogości ofert pracy, niższym progiem wejścia jeśli znasz już JavaScript, a także bogatego ekosystemu.

![meme_cool.gif](/assets/img/blog/meme_cool.gif)

### Inne

Temat alternatywnych technologii nie jest wyczerpany. Istnieje wiele innych bibliotek, czy frameworków które rozwiązują podobny problem co Flutter, jednak jest to już "drobnica" dla zapaleńców, czy programistów hobbystycznych (bez obrazy). Jeżeli zależy Ci na technologii która będzie rozwijana i wspierana przez najbliższe lata - doświadczenie jasno mi podpowiada, że należy postawić na rozwiązanie za którym stoi dedykowany sztab techniczny dużej firmy (bo tylko taka może sobie na to pozwolić), a nie za frameworkiem krzakiem, który może się pochwalić jedynie ładnym landing-page, a możliwości i jakiekolwiek wsparcie kończy się tuż po zainstalowaniu paczki na naszym lokalnym środowisku. Stwierdzenie brutalne, lecz prawdziwe.

## Flutter

Skoro wiemy już czym są aplikacje cross-platformowe i wiemy, że problem został już dawno rozwiązany - to co robi tu Flutter? Jakie karty wnosi do gry i do kogo dokładnie jest skierowany? Mamy już przecież młotek do szybkiego pisania wolnych aplikacji (Ionic), do pisania w ekosystemie typu enterprise (Xamarin), czy do tworzenia niemal natywnych tworów z użyciem języka skryptowego (React Native). Co jeszcze można tutaj ugrać i jakim asem zagrać? Zaczynajmy.

### Język

- *Co napędza Fluttera?*
- **Dart**
- *W sensie to taki język programowania?*
- **Tak**
- ...

Mogłeś nie słyszeć o tym języku, nawet jeśli nie spędziłeś ostatnich miesięcy pod kamieniem. Nie należy do najpopularniejszych narzędzi, zwłaszcza że przed Flutterem nie miał realnego i mainstreamowego zastosowania. Według rankingu TIOBE, który śledzi popularność dostępnych języków, popularniejsze są m.in. Fortran, Scratch, czy nawet COBOL. Zaryzykuję wręcz stwierdzenie że Dart to taki trochę eksperyment developerski, który dojrzewał w piwnicy na swój moment chwały.

![dart_rank_2019.png](/assets/img/blog/dart_rank_2019.png)


Składnia języka jest nowoczesna, prawdopodobnie za sprawą tego, że debiutował w 2011 roku - nie miał czasu się zestarzeć. Mógłbym napisać, że to mieszanka Javy i JavaScriptu, ale zdaję sobie sprawę jaka będzie Twoja reakcja na takie porównanie. 

![meme_moron.gif](/assets/img/blog/meme_moron.gif)

Ujmę to więc inaczej:

```dart
void main() {
  List<String> keywords = ["Rykowski", "ma"];
  keywords.add("psa");
  keywords.forEach((keyword) => print(keyword));
  print("Lista ma ${keywords.length} elementów");
}
```

Wygląda jak zwykły język programowania? Otóż to. Im lepiej poznasz język, tym bardziej docenisz jego prostotę i brak niespójności, a zarazem to że działa w sposób oczekiwany i intuicyjny. Zdaję sobie  sprawę, że trudno oceniać język po kilku linijkach kodu - obiecuję opracować dedykowany wpis samemu językowi.

Warto wspomnieć, że Dart został zaprojektowany przez tą samą firmę która odpowiada za Fluttera, czyli Google. Co to oznacza, poza tym że mamy tutaj interesujący zbieg okoliczności? Z reguły języki programowania rozwijają się swoim rytmem i planem, wprowadzają zmiany i udogodnienia dla programistów, jednak każda taka zmiana jest mocno ważona i jej wprowadzenie zawsze zajmuje sporo czasu. W naszym przypadku jedna firma steruje zarówno językiem jak i biblioteką która go wykorzystuje - stąd część zmian jest mocno sterowana potrzebami frameworka. Zamknięty ekosystem wzajemnie się napędza i dostarcza wysokiej jakości narzędzie do wykorzystania przez programistów aplikacji.

### Interfejs użytkownika (UI)

Interfejs użytkownika, czyli to co widzi użytkownik naszej aplikacji. Przyciski, przełączniki, formularze, a nawet wyświetlany tekst zawierają się w interfejsie. Flutter wprowadza sporo zamieszania i nowości w tym konkretnym temacie. Wszystko za sprawą własnego silnika renderującego Skia, który w całości odpowiada za to co w danym momencie jest wyświetlane na ekranie. Piksel po pikselu Flutter steruje widocznymi elementami, a także animacją odpowiedzialną za płynne odpowiadanie na interakcje użytkownika.

Co w praktyce oznacza, że Flutter sam rysuje wszystko co jest widoczne w aplikacji? Czy inne biblioteki nie robią tego samego? **Nie**. Komponenty w Ionicu rysowane są przez przeglądarkę systemu - co oznacza, że będą wyglądały inaczej w zależności od tego jaka przeglądarka, a nawet wersja jest aktualnie zainstalowana na telefonie. React Native oraz Xamarin rysują natywne komponenty systemowe - z jednej strony dają nam one poczucie korzystania z aplikacji natywnej, z drugiej - trudno jest sprawić aby aplikacja wyglądała identycznie na obu platformach. Flutter sprawia, że każdy komponent będzie wyglądał identycznie na każdym urządzeniu - niezależnie od tego czy pracujemy na Androidzie, iOS-ie, a nawet przeglądarce webowej.

Kwestia wbudowanych komponentów graficznych które oddane są do naszej dyspozycji robi robotę. Nie będę ich tutaj opisywał, ograniczę się wyłącznie do faktu, że Flutter posiada pełne i oficjalne wsparcie dla Material Design (znowu Google). Wiesz co to znaczy, prawda? Setki prostych i stylowych elementów do wykorzystania, z czego każdy można ostylować do własnych potrzeb poprzez dedykowany obiekt styli. **Wow!**

![flutter_ui.gif](/assets/img/blog/flutter_ui.gif)
*UI aplikacji The History of Everything*

### Wydajność

Aplikacja podczas budowania jest kompilowana do kodu natywnego, więc nie ma się czym martwić jeśli chodzi o wydajność. Elementy renderowane są błyskawicznie, listy nie lagują podczas szybkiego scrollowania, a animacje są płynne i bez problemów osiągają mityczne 60fps. Nie zdarzyła mi się osobiście jeszcze sytuacja, w której miałbym problem natury "wydolnościowej". Koniecznie muszę jednak zaznaczyć, że aplikacja działa w pełni płynnie dopiero gdy zbudujemy ją w trybie release - czyli takim w jakim dostanie ją użytkownik końcowy. W przypadku gdy pracujemy w trybie developerskim (hot-reloading), aplikacja działa poprawnie ale bez efektu wow - warto mieć to na uwadze, gdy rozpoczniemy swoją przygodę.

### Ekosystem i społeczność

Każda nowa technologia potrzebuje w skrócie dwóch rzeczy aby osiągnąć sukces:

- Wysoka jakość
- Rozbudowany ekosystem

O ile kwestii wysokiej jakości nie ma potrzeby opisywać - słaba technologia się sama nie obroni i umrze śmiercią naturalną, to ekosystem jest rzeczą której warto się przyjrzeć. Jest to cecha nad którą Google nie ma pełnej kontroli - o ile jest w stanie dopieszczać Fluttera o nowe mechanizmy i rozwiązane błędy, to społeczność buduje się kilka kroków dalej - samodzielnie. Rozbudowany ekosystem wymaga czasu - konieczne jest zebranie odpowiedniej masy krytycznej developerów, którzy poświęcą część swojego czasu na ewangelizację technologii. Przyjmuje ona różne formy:

- Gotowe biblioteki
- Prezentacje i prelekcje
- Pomoc w rozwiązywaniu problemów początkującym

Ekosystem nie jest jeszcze ustabilizowany - technologia jest młoda i nie miała jeszcze czasu tego wypracować. Istnieje co prawda wiele gotowych rozwiązań od community, jednak duża część zdążyła zostać już porzucona przez właścicieli - na próżno więc liczyć na poprawki, a lista zgłaszanych błędów wciąż rośnie. Mimo tego, że nie jest idealnie (w porównaniu choćby z React Native), to z każdym miesiącem sytuacja się polepsza. Osobiście wierzę, że rok 2019 będzie przełomowym i jako programiści będziemy mogli się cieszyć z rozbudowanego ekosystemu.

### Dokumentacja

Oficjalne źródło wiedzy, które jest nieocenioną częścią samej technologii. Co z tego, że narzędzie jest świetne i najlepsze na świecie, skoro dokumentacja napisana została byle jak, a dodatkowo brakuje w niej wielu informacji i wytycznych w jaki sposób się nim posługiwać? Na nasze szczęście Google słynie z tego, że jego produkty posiadają świetną i obszerną dokumentację techniczną. Każdy niemal aspekt jest skrupulatnie i dokładnie rozpisany, a poza standardowym *API reference*, znajdziemy tutaj m.in:

- Najlepsze praktyki dotyczące testowania i debugowania aplikacji
- Ścieżki wdrożeniowe dla programistów innych technologii (w tym React Native, czy Web)
- Wysokopoziomowe poradniki dotyczące layoutów, interakcji, czy sterowania animacjami
- W jaki sposób zbudować aplikację produkcyjną wraz z Continuous Deployment

Jestem mocno zaskoczony - pozytywnie - że tak młoda technologia jak Flutter posiada tak rozbudowaną i obszerną dokumentację na każdy istotny temat. Jeśli chcesz dogłębnie poznać dowolne zagadnienie z zakresu frameworka, znajdziesz je właśnie w oficjalnej dokumentacji, która dostępna jest pod adresem <https://flutter.dev/docs>

### Czy warto?

Przy nowych technologiach pojawia się zawsze to samo pytanie - czy warto się nią zainteresować i czy w długiej perspektywie czas spędzony na nauce się zwróci (mówię rzecz jasna o $$$)? Nie mam jednoznacznej odpowiedzi na to pytanie, nie wezmę odpowiedzialności za to jak inwestujesz swój czas. Wszystko jednak wskazuje na to, że Flutter zostanie na rynku długo i warto wskoczyć już teraz do jego wagonika. *Early adopters would be rewarded*.
