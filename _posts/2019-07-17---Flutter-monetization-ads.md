---
title: Zarabianie i monetyzacja - Reklamy AdMob
date: "2019-07-17"
layout: "post"
slug: "flutter-monetization-admob"
excerpt: "Darmowe aplikacje mobilne również na siebie zarabiają. W jaki sposób? Istnieje wiele strategii monetyzacyjnych, a jedną z nich są reklamy AdMob w różnych wariantach. Pierwszy krok w zarabianiu na aplikacji pisanej we Flutter."
---

Pisanie aplikacji mobilnych to świetna zabawa. Tworzenie czegoś z myślą, że będzie używane przez setki, tysiace, a nawet miliony ludzi pobierajacych naszą apkę ze sklepów to mieszanka dumy, ekscytacji i czystego fun-u. Tak to wygląda przynajmiej z naszej strony - czysto technicznej. Wypuszczamy aplikację, dopieszczamy ją o nowe funkcjonalności i rozwiązujemy najbardziej krytyczne, lub uciążliwe błędy. Zastanawiałeś się jednak czy jest coś więcej? Coś ważniejszego?

> Druga strona medalu - strona biznesowa.

Może to zabrzmieć brutalnie, zwłaszcza z ust programisty - przecież jestem jednym z Was! **Warstwa biznesowa jest stroną nadrzędna, a technologia powinna służyć biznesowi w osiąganiu wyznaczonych celów**, nie na odwrót. Jakie cele może mieć biznes? Bo cele techniczne są nam w większości znane:

1. Czysty i schludny kod
2. Korzystanie z nowych narzędzi/frameworków
3. Posiadanie dużego pokrycia testami
4. Automatyzacja powtarzających się czynności (np. build aplikacji)
5. I wiele, wiele więcej.

> Biznes ma jeden kluczowy cel - zarabianie $$$. To między innymi z tych środków bierze się Twoja i moja wypłata.

## Sposoby monetyzacji

O tym właśnie przeczytasz w tym wpisie. O zarabianiu na aplikacjach mobilnych, czyli jak monetyzować użytkowników. O tym w jaki sposób "wycisnąć" trochę grosza, nawet z hobbistycznego projektu robionego po godzinach. W sposób etyczny i co ważniejsze nienachalny, abyś nie wstydził się swojego dzieła przed (nie)znajomymi. W jaki sposób można zarabiać na aplikacji? W przeróżny taki jak:

- sprzedaż danych o użytkowniku
- kopanie kryptowaluty
- mikropłatności

Najłatwiejszym jednak środkiem zarabiania, jeśli przyjmiemy że głównym kryterium doboru jest zainwestowany czas w implementację okazują się reklamy. Jest to również często pierwsza myśl, gdy zaczynasz myśleć o jakiejkolwiek monetyzacji - czy to aplikacja webowa, czy mobilna. Pomimo faktu, że nikt ich nie lubi, to **Polsat** od lat korzysta z tego patentu i ma się całkiem nieźle. Podstawa to umiejętne nimi zarządzanie i używanie we właściwym momencie. Dlaczego akurat reklamy, a nie którakolwiek pozycja z powyższej listy?

1. Sprzedaż danych o użytkowniku wymaga abyś dane te gdzieś zbierał, przetwarzał i miał klienta który chętnie za nie zapłaci. Problemem jest fakt, że nie masz takiego klienta. No i wynalazek w stylu **RODO** mocno komplikuje cały proceder.
2. Kopanie kryptowaluty na telefonie użytkownika jest mało wydajne, zajeżdza baterię, a przede wszystkim jest mało etyczne - chyba, że go powiadomisz o tym fakcie, ale wtedy pewnie odinstaluje aplikację. Kto celowo chciałby dołączyć do farmy zombie?
3. Mikropłatości uważam za świetną strategię monetyzacyjną, jednak wpierw trzeba mieć na tyle dobry produkt, aby móc przekonać użytkownika do barteru **twarda waluta -> wirtualne dobro**. Myślisz że Twój aktualny produkt jest na tyle dobry, aby sprzedawać wirtualne złote monety komuś innemu niż mamie i tacie?

Mamy pełną jasność - zaczynamy od reklam. Reklama reklamie jednak nierówna, o czym przekonasz się w nieco dalszej części wpisu.

![logo.png](/assets/img/blog/admob/logo.png)

## Zrozumieć reklamy

Wyświetlanie reklam w aplikacji mobilnej opiera się na udostępnianiu określonej przestrzeni ekranu reklamodawcy, który wstawi w to miejsce odpowiednią wizualizację. Zupełnie jak w świecie rzeczywisitym - reklamodawca wynajmuje przestrzeń reklamową np. bilboard przy drodze szybkiego ruchu i rozwiesza na nim swój plakat reklamujący nową Hondę, czy też firma, która wykupuje od pewnej stacji telewizyjnej czas antenowy, podczas którego zajmie ekran widza swoim krótkim spotem.

Wszystko fajnie, koncept jest stosunkowo jasny. Pozostaje jedynie kwestia **znalezienia reklamodawcy**, który zechciałby rozwiesić u nas swój "plakat reklamowy". Nie musimy na szczęście szukać zainteresowanej firmy na własną rękę, dzięki czemu odpada nam konieczność implementacji własnego silnika reklamowego, rozliczeniowego i miliona innych zbędnych z punktu widzenia aplikacji funkcjonalności. Wystarczy wybrać jednego z istniejących dostawców reklam, zintegrować się z jego SDK i zacząć wyświetlać reklamy.

## Dostawcy reklam (sieć reklamowa)

Wybrać dostawcę? Ale którego? Na rynku dostępne są dziesiątki dostawców - mniejszych i większych - spośród których można wybrać tego najlepszego. A najlepszy to ten, który płaci najwięcej za wyświetlenia. **Nie tylko.** Uwzględnijmy kilka pomniejszych kwestii które koniecznie należy mieć na uwadze:
 
 1. **Wypłacalność dostawcy**. Szkoda byłoby zbierać wirtualne złotówki przez kilka miesięcy, a w momencie wypłaty dowiedzieć się, że nic się nie dzieje i nasze środki pozostaną wirtualne na wieki.
 2. Istnieje **szczegółowa dokumentacja** opisująca proces integracyjny. Oszczędzi nam to czasu na osadzanie reklam metodą prób i błędów, gdzie nigdy nie mamy pewności że wszystko zrobiliśmy jak należy.
 3. Dojrzałe i **rozbudowane SDK**. Takie które umożliwi nam wyświetlanie różnych rodzajów kreacji reklamowych (o tym za chwilę), czy możliwość sterowania dopasowaniem reklamy. Jeśli masz aplikację o tematyce motoryzacyjnej to dopasowana reklama będzie dotyczyć m.in. nowej Hondy, a nie leku na hemoroidy.
 4. **Fill rate**, czyli duża ilość reklamodawców wewnątrz sieci. Dostawca nie osadza własnych reklam, lecz reklamy zlecone przez reklamodawców. Im większa liczba takich reklamodawców, tym większa szansa na to, że będziemy mieli jakąkolwiek reklamę do wyświetlenia. Co nam z tego, że dostawca płaci 3x więcej niż konkurencja, skoro reklamę wyświetlimy 1 na 100 użytkowników. Przykładowo Pepsi zleca kampanię reklamową przez AdMob, a AdMob zleca swoim partnerom (Tobie) wyświetlanie reklamy.

Kwestii tego typu jest zdecydowanie więcej, dlatego aby oszczędzić nam obojgu czasu przejdę już do sedna. Skorzystaj z **AdMob**. Jest to sieć reklamowa, której właścicielem jest **Google** (tak samo jak Fluttera), istnieje na rynku niemal od zawsze i jest rozwiązaniem, którego sam używam w swoich aplikacjach. Bardziej zarekomendować nie potrafię - **Wszyscy mają AdMob - mam i ja**.

## Konto w AdMob

Rozpoczęcie dowolnej integracji wymaga niemal zawsze utworzenia stosownego konta na platformie docelowej. Nie inaczej jest w przypadku AdMoba - konto wymagane, nic nie poradzisz. Proces jest prosty, niefinezyjny, nie warto nad nim dogłębnie rozpisywać - do brzegu!

1. Przejdź na stronę [https://admob.google.com/home/](https://admob.google.com/home/) i kliknij w przycisk **Sign up**
2. Zaloguj się swoim kontem Google i wypełnij krótki formularz:
    - Country or territory: Poland
    - Time zone: (UTC+01:00) Prague
    - Billing currency: Polish Zloty (PLN)
    - Zaakceptuj regulamin (po ewentualnym przeczytaniu)
3. Odpowiedz *No* na wszystkie pytania o ~~spam~~ mailing
4. Podaj numer telefonu w celu weryfikacji (preferuję SMS) - nie zostaną naliczone żadne koszty.
5. Konto w platformie utworzone, zostałeś **partnerem**.

Masz już własne konto, ale to za mało. Musisz dodatkowo stworzyć nową aplikację z poziomu panelu administracyjnego. W tym celu z bocznego menu wybierz **Aplikacje > Dodaj aplikację** i postępuj zgodnie z krokami formularza. Podaj nazwę swojej aplikacji oraz docelową platformę. **Zaraz, zaraz.** Jak to platformę? Przecież aplikacja jest pisana we Flutterze, więc cross-platformowa i dostępna na obu. Oczywiście! W takim celu musisz stworzyć dwie osobne aplikacje w panelu - jedną dla Android, drugą dla iOS.

Po podaniu podstawowych danych, możesz przejść do sekcji **Jednostki reklamowe > Dodaj jednostkę reklamową**, aby tworzyć tzw. **jednostki reklamowe**. Jest to jednak krok opcjonalny, na czas wpisu będziemy korzystali z ogólnodostępnych identyfikatorów testowych.

## Jednostki reklamowe

Jednostka reklamowa to bezpośredni element, który osadzasz w aplikacji mobilnej. Każda jednostka posiada dwie podstawowe cechy: unikalny identyfikator oraz typ. Identyfikatora użyjesz w momencie wyświetlania reklamy, tak aby dostawca reklamowy wiedział, że to Ty osadzasz daną reklamę i należą Ci się za to pieniądze. Typ kreacji to taki **enum**, który przyjmuje jedną z trzech wartości: baner, reklama pełnoekranowa, reklama nagradzająca. O tym czym się różnią, jak i kiedy ich używać dowiesz się w kolejnej sekcji.

## Praktyka

Teoria reklamowa za nami, pora przetestować je na żyjącym organizmie. Osadźmy błyszczący baner w aplikacji i zacznijmy zarabiać (nie)godziwe pieniądze! **STOP**. Pozostał jeszcze jeden krok aby móc eksperymentować z jednostkami reklamowymi - musimy przeprowadzić wstępną konfiguracjo-integrację:

### Wstępna konfiguracja

1. W pliku *pubspec.yaml* dodaj nową zależność i zainstaluj ją:
```yaml
dependencies:
  firebase_admob: 0.9.0+1
```

Nie wiesz jak zainstalować dodaną zależność? W konsoli przejdź do katalogu projektu i wykonaj następującą komendę:

```bash
flutter pub get
```

2. *[Android]* W pliku **android/app/src/main/AndroidManifest.xml** w obrębie węzła *\<application\>* dodaj informację o identyfikatorze aplikacji z AdMob. Zastąp `[ADMOB_APP_ID]` wartością z panelu AdMob, którą znajdziesz wybierając aplikację z listy i przechodząc do sekcji jej ustawień.

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="[ADMOB_APP_ID]" />
```

![admob_dashboard.png](/assets/img/blog/admob/admob_dashboard.png)

Jeśli masz problem z poprawnym umiejscowieniem znacznika, lub nie masz pewności czy wstawiłeś go w odpowiednim miejscu - spójrz na przykładowy plik [AndroidManifest.xml](https://github.com/vintage/flutter_demo_ads/blob/master/android/app/src/main/AndroidManifest.xml#L13:L15).

3. *[iOS]* W pliku **ios/Runner/Info.plist** dodaj nową parę klucz-wartość. Zastąp `[ADMOB_APP_ID]` wartością z panelu AdMob, którą znajdziesz w identyczny sposób jak w przypadku konfiguracji Androida.

```xml
<key>GADApplicationIdentifier</key>
<string>[ADMOB_APP_ID]</string>
```

Przykładowy plik [Info.plist](https://github.com/vintage/flutter_demo_ads/blob/master/ios/Runner/Info.plist#L44:L45).

> Pamiętaj aby podając identyfikatory aplikacji, platformy zgadzały się z tym co jest zapisane w panelu. Jeśli przykładowo w pliku *AndroidManifest.xml* podasz klucz pasujący do platformy *iOS* to aplikacja może przestać się uruchamiać.

4. Przed rozpoczęciem osadzania reklam musisz koniecznie zainicjalizować klienta reklamowego. Najprościej jest to zrobić wewnątrz metody `main` w pliku **lib/main.dart**.

```dart
import 'dart:io' show Platform;
import 'package:flutter/material.dart';
import 'package:firebase_admob/firebase_admob.dart';

const ANDROID_KEY = "[ANDROID_KEY]";
const IOS_KEY = "[IOS_KEY]";

void main() {
  String appId = Platform.isAndroid ? ANDROID_KEY : IOS_KEY;
  FirebaseAdMob.instance.initialize(appId: appId);
  
  runApp(App());
}

// ...
```

Gdzie `[ANDROID_KEY]` i `[IOS_KEY]` muszą zostać podmienione na identyczne wartości jakich użyłeś odpowiednio w plikach *AndroidManifest.xml* i *Info.plist*. Przykładowy [main.dart](https://github.com/vintage/flutter_demo_ads/blob/master/lib/main.dart#L1-L14).

Jeśli chodzi o wstępną konfigurację - mamy to. Jesteśmy teraz w pełni przygotowani, aby wskoczyć do kodu Fluttera i rozpocząć pisanie aplikacji demonstracyjno-reklamowej.

### Layout aplikacji

Layout aplikacji będzie się składać początkowo z trzech przycisków, gdzie każdy jest odpowiedzialny za wyświetlenie reklamowy odpowiedniego typu. Żadego fancy stylowania, surowy widok umożliwiający nam rozegranie partii z reklamami. 

```dart
class App extends StatefulWidget {
  @override
  _AppState createState() => _AppState();
}

class _AppState extends State<App> {
  void showBanner() {}
  void showInterstitial() {}
  void showRewardVideo() {}

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'AdMob demo',
      home: Scaffold(
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              RaisedButton(
                child: Text("Show banner"),
                onPressed: showBanner,
              ),
              RaisedButton(
                child: Text("Show interstitial"),
                onPressed: showInterstitial,
              ),
              RaisedButton(
                child: Text("Show reward video"),
                onPressed: showRewardVideo,
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

**App** od samego początku implementowany jest jako widget stanowy - ~~czuję~~ wiem, że im dalej w las tym bardziej będziemy potrzebowali zarządzać stanem. Jeśli chodzi o sam layout to jest on prosty w opór. Trzy przyciski na środku ekranu wołające powiązane funkcje, które aktualnie nic nie robią. A skoro nic nie robią to na pewno nie wyświetlają też reklam. **Jeszcze!**

![layout.png](/assets/img/blog/admob/layout.png)

## Wyświetlanie reklam

Wyświetlanie reklamy - nie zależnie od jej typu - zawsze opiera się o identyczny scenariusz jeśli chodzi o implementację. Różnią się szczegóły, detale takie jak kiedy dokładnie pokażemy reklamę. To jednak problem czysto UX. Od strony kodu wyróżniamy dwa główne kroki do osiągnięcia celu:

1. Załadowanie reklamy (**load**) - pierwszą rzeczą jaką musimy zrobić jest wysłanie żądania do reklamodawcy (AdMob), żeby dostarczył nam kreację określonego typu. Wołamy reklamodawcę i informujemy go, że w niedługim czasie będziemy chcieli pokazać przykładowo baner i niech nam dostarczy odpowiednią wizualizację. Operacja albo się uda, albo i nie. Od czego zależy sukces? Przede wszystkim od dostępności połączenia z internetem (nie ma internetu, nie ma reklam) oraz **fill rate**, który omawialiśmy wyżej.
2. Wyświetlenie reklamy (**show**) - gdy reklama zostanie poprawnie załadowana nie jest automatycznie prezentowana na ekranie. Musimy sami podjąć kolejną akcję, aby pokazać ją użytkownikowi. Jest to akcja typu *"czysta formalność"* - konkretna kreacja zostałą już zaciągnięta na telefon użytkownika i oczekuje jedynie sygnału do zmaterializowania się na ekranie.

### Baner (Banner)

Baner reklamowy, czyli pasek na pełną szerokość ekranu i określoną wysokość (32, 50, lub 90 pikseli). Wyświetlany najczęśćiej na samym dole aplikacji. Z punktu widzenia osadzania tej jednostki reklamowej - najprościej jest zlecić dobranie rozmiaru SDK, tak aby określiło odpowiednią wysokość banera (**smart size**). Brana jest pod uwagę w tym wypadku rozdzielczość ekranu, tak aby pomimo stale wyświetlanej reklamy zapewnić jak największy komfort użytkowania. Jeśli masz własną wizję lub nietypowy design wymagający określonego banera to możesz to prosto skonfigurować pod własne potrzeby. Jednostka charakteryzuje się łatwością osadzenia - przy starcie aplikacji wystarczy załadować reklamę i ją wyświetlić. Sama będzie zmieniała swoją zawartośc co określony interwał (**rotacja reklamy**), a my nie musimy się martwić niczym więcej.

![banner.png](/assets/img/blog/admob/banner.png)

W aplikacji przyjęliśmy wstępny scenariusz, że to użytkownik klika w przycisk, aby zobaczyć baner reklamowy. Tak też zróbmy poprzez odpowiednią implementację powiązanej metody:

```dart
void showBanner() async {
  var banner = BannerAd(
    adUnitId: BannerAd.testAdUnitId,
    targetingInfo: MobileAdTargetingInfo(),
    size: AdSize.smartBanner,
  );
  banner.listener = (MobileAdEvent event) {
    if (event == MobileAdEvent.loaded) {
      banner.show();
    }
  };
  banner.load();
}
```

Sporo się tutaj dzieje jak na pierwszy raz - tak przynajmniej to wygląda. W rzeczywistości to **żaden rocket science**!.

```dart
var banner = BannerAd(
  adUnitId: BannerAd.testAdUnitId,
  targetingInfo: MobileAdTargetingInfo(),
  size: AdSize.smartBanner,
);
```

Tworzymy nową instancję typu **BannerAd**, czyli baner reklamowy. Interesujące są argumenty które dostarczamy:

- **adUnitId** to identyfikator jednostki reklamowej do wyświetlenia. Najważniejszy z parametrów, bo to on odpowiada czy wyświetlasz swoją reklamę za którą masz płacone, czy np. sąsiada z bloku obok (też jest programistą?). Aktualna wersja kodu wykorzystuje zdefiniowaną stałą, która wyświetli baner testowy (bezpieczny, bo można go klikać do woli bez ryzyka zbanowania).
- **targetingInfo** pozwala na określenie dodatkowych wytycznych odnośnie samej reklamy, zawężenie grupy odbiorców. Możemy zdefiniować, że aplikacji używają głównie dzieci i dostaniemy reklamy wyłącznie z określonej puli. Możemy także podać listę słów kluczowych, które wiemy że pasują do obecnego użytkownika, aby zaprezentować mu lepiej dopasowaną reklamę. **WIN-WIN**. Ewentualnie nie definiować nic (co też robimy) - Google i tak wie wiele o użytkowniku z własnych źródeł.
- **size** określa jak się spodziewasz rozmiar reklamy. Z własnego doświadczenia polecam zawsze korzystać z **AdSize.smartBanner**, szkoda czasu i życia na zastanawianie się samemu jaki rozmiar dobrać, aby był adekwatny do urządzenia użytkownika. Parametr występuje tylko na reklamach typu baner.

```dart
banner.listener = (MobileAdEvent event) {
  if (event == MobileAdEvent.loaded) {
    banner.show();
  }
};
```

Rozpoczynamy nasłuchiwanie na wszelkie zdarzenia które przytrafią się naszemu nowo utworzonemu banerowi. Z praktycznego punktu widzenia interesuje nas jeden konkretny - **MobileAdEvent.loaded** - który otrzymamy, gdy tylko reklama zostanie poprawnie załadowana na urządzenie. W tym momencie chcemy zrobić już tylko jedną rzecz - pokazać reklamę na ekranie. **PROFIT**.

```dart
banner.load();
```

Rozpoczęcie procesu ładowania reklamy. Nie dostarczamy tutaj żadnych argumentów - wszystkie istotne dane zostały już określone w momencie konstruowania instancji banera. Cała obsługa tego co dzieje się podczas/po załadowaniu wizualizacji odbywa się bezpośrednio w listenerze zdefiniowanym wyżej.

Uruchom aplikację i stuknij w przycisk **Show banner**. Początkowo nic się nie zadzieje, jednak po maksymalnie paru sekundach zobaczysz na dole ekranu baner z reklamą testową. **Sukces!** Jedyny problem jaki pozostał do rozwiązania to nienaturalność. Baner jest najczęściej ładowany i pokazywany tuż po uruchomieniu aplikacji - użytkownik nie podejmuje żadnej akcji z tym związanej. Wyrzućmy dedykowany przycisk, a funkcję **showBanner** zawołajmy bezpośrednio w metodzie **initState**, aby baner pokazał się tak szybko jak to tylko możliwe.

```dart
class _AppState extends State<App> {
  @override
  void initState() {
    showBanner();
    super.initState();
  }
  // ...
}
```

> SDK umożliwia pokazanie kilku banerów na jednym ekranie (np. góra+dół), jednak jest to niezgodne z polityką AdMob. Nie rób tego, jeśli nie chcesz zostać zbanowany razem ze zgromadzonymi środkami. Pamiętaj - w jednym czasie na ekranie może być widoczny tylko jeden baner!

### Reklama pełnoekranowa (Interstitial)

Wariant pełnoekranowego modala całkowicie przykrywającego zawartość aplikacji. W przeciwieństwie do banera nie jest widoczny przez cały czas i to od Ciebie zależy kiedy go pokażesz - lepiej nie na starcie aplikacji bo cuchnie to tandetą i skutkuje błyskawicznym odinstalowaniem aplikacji z telefonu. Idealnym momentem są za to tzw. **przerywniki**. Moment w którym kończy się pewien proces/etap jak np. przejście poziomu w grze, czy odznaczenie wszystkich pozycji na wirtualnej liście jako ukończone. Wszystko zależy od konkretnej aplikacji i znalezienia złotego (ewentualnie srebrnego) punktu w którym można się wpasować z reklamą.

![interstitial.png](/assets/img/blog/admob/interstitial.png)

Warto pamiętać o tym, że lepiej pokazać reklamę użytkownikowi zadowolonemu, niż sfrustrowanemu. Stoi za tym prosty powód i wytłumaczenie. Obejrzenie reklamy obniża poziom ogólnie przyjętego szczęścia, jakakolwiek by ona nie była. **Nikt nie lubi reklam**. Jeśli więc postanowiłeś, że pokażesz reklamę użytkownikowi za każdym razem gdy błędnie wypełni formularz - przemyśl to. Przeleje się czara goryczy, a użytkownik wystawi Ci niepochlebną opinię w sklepie i odinstaluję aplikację na wieki wieków. **Amen**.

Analogicznie jak w przypadku banera - chcemy zaprezentować reklamę użytkownikowi po stuknięciu w przycisk. Tym razem jednak zamiast małego banera, wyświetlimy pełnoekranową jednostkę która zasłoni cały dotychczasowy widok.

```dart
void showInterstitial() {
  var interstitial = InterstitialAd(
    adUnitId: InterstitialAd.testAdUnitId,
    targetingInfo: MobileAdTargetingInfo(),
  );
  interstitial.listener = (MobileAdEvent event) {
    if (event == MobileAdEvent.loaded) {
      interstitial.show();
    }
  };
  interstitial.load();
}
```

Kod wygląda jakby znajomo prawda? To niemal kalka z funkcji `showBanner`! Tworzymy instancję reklamy `InterstitialAd`, zaczynamy nasłuchiwać zdarzeń i wysyłamy żądanie ładowania. Nic co warte byłoby dodatkowego opisu. Główna różnica polega na tym, że nie definiujemy rozmiaru kreacji. Dlaczego? Bo reklama pełnoekranowa jest pełnoekranowa aka zajmuje pełną dostępną przestrzeń ekranu. **Obvious**.

Wstępna implementacja gotowa. Kliknięcie w przycisk rozpoczyna ładowanie reklamy, po czym automatycznie pojawia się na ekranie. Niby fajnie, ale znowu - nie życiowo. Nie planuję komplikować oczywiście naszej aplikacji, aby użycie kreacji tego typu miało 100% sens, jednak szczypta zmian jest mile widziana. Pamiętasz kiedy jest dobry moment na pokazanie reklamy pełnoekranowej? Gdy użytkownik zamyka pewien proces np. wygrywa kolejny poziom w grze.

Zasymulujmy w opór prostą grę, która będzie polegała na wyborze z dostępnych kwadratów tego który ma kolor czerwony. Wiem, wiem - brzmi głupio, ale lepiej obrazuje **sposób** wykorzystania reklamy tego typu. Zastąp przycisk pokazujący reklamę następującym kodem, który wyświetli 4 kwadraty zielone i 1 czerwony. Kliknięcie na czerwony oznacza zwycięstwo i przejście na kolejny poziom + reklama.

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: boxes,
)
```

Dostępne kwadraty trzymane są w stanie aplikacji, aby w momencie przejścia na kolejny poziom móc je zaktualizować. Zainicjalizujmy wstępnie listę wartościami.

```dart
class _AppState extends State<App> {
  List<Widget> boxes = [];
  
  @override
  void initState() {
    boxes = getInitialBoxes();
    // ...
  }
  
  List<Widget> getInitialBoxes() {
    return [
      Container(width: 50, height: 50, color: Colors.green),
      Container(width: 50, height: 50, color: Colors.green),
      GestureDetector(
        child: Container(width: 50, height: 50, color: Colors.red),
        onTap: showInterstitial,
      ),
      Container(width: 50, height: 50, color: Colors.green),
      Container(width: 50, height: 50, color: Colors.green),
    ];
  }
}
```

![boxes_demo.gif](/assets/img/blog/admob/boxes_demo.gif)

To jednak nie wszystko. Chcemy też mieć pewność **kiedy dokładnie** wyświetli się reklama - z dokładnością do teraz, a nie za sekundę-dwie. Aby to osiągnąć musimy załadować reklamę na początku gry, a w odpowiednim czasie jedynie ją wyświetlić. Wymaga to trochę zmian w kodzie aplikacji. Po pierwsze załadujmy reklamę na samym starcie, jednak bez listenera który pokaże ją gdy tylko będzie dostępna.

```dart
class _AppState extends State<App> {
  InterstitialAd interstitial;
  
  @override
  void initState() {
    interstitial = InterstitialAd(
      adUnitId: InterstitialAd.testAdUnitId,
      targetingInfo: MobileAdTargetingInfo(),
    );
    interstitial.load();
    // ...
  }
}
```

Dodatkowo niezbędna jest zmiana funkcji `showInterstitial`, aby nie tworzyła obiektu reklamy do załadowania - kroki te zostały już wykonane na starcie. Jedyna odpowiedzialność tej funkcji to wyświetlenie reklamy. Po pokazaniu reklamy chcemy również wygenerować nową planszę w sposób pseudolosowy. Brzmi fancy, w rzeczywistości ograniczymy się do przetasowania elementów - nie gwarantujemy, że po przetasowaniu nie trafimy znowu na ten sam rozkład.

```dart
void showInterstitial() {
  setState(() {
    boxes.shuffle();
  });
  interstitial.show();
}
```

Uruchom aplikację ponownie i wciel się w rolę gracza. Przez "*wciel się*" mam na myśli fakt, abyś udawał że zastanawiasz się który z kwadratów jest czerwony (chyba że poważnie masz z tym problem) - daj czas reklamie na załadowanie się w tle. Wybierz odpowiedni kwadracik i gotowe - reklama wyskakuje zanim zdążysz mrugnąć. Świetnie, ale co z graczem który od razu kliknie w dobry kwadracik? **Nic**. Z reguły proces podczas którego ładujesz reklamę jest dłuższy niż czas potrzebny na jej załadowanie - wyimaginowany problem. A nawet jeśli się zdarzy to najłatwiej się z tym po prostu pogodzić - czasem użytkownik nie zobaczy reklamy. Jest to zdecydowanie lepsze rozwiązanie niż blokowanie możliwości przejścia procesu póki nie będziesz miał gotowej reklamy.

Jeśli przy testowaniu byłeś odpowiednio ciekawski to zauważyłeś prawdopodobnie pewien intrygujący fakt. Pierwsze zwycięstwo rzeczywiście wyświetla reklamę, jednak po jej zamknięciu i ponownej wygranej nic się dzieje. **Brak reklamy to brak profitu.** Dlaczego działa tylko w pierwszej próbie? Powód jest prozaiczny. Reklamę tworzysz i ładujesz tylko raz przy starcie. Jeśli pokażesz ją użytkownikowi to koniec, nie ma więcej. Nie możesz pokazać dwa razy tej samej załadowanej kreacji.

Rozwiązaniem jest obsłużenie zdarzenia zamknięcia reklamy i utworzenie w tym momencie nowej instancji reklamowej. W takim przypadku pokażesz użytkownikowi aktualną reklamę, a tuż po zamknięciu zaczniesz ładować kolejną kreacją na przyszłość. Proste i błyskotliwe - takie rozwiązania lubię i polecam. Wymaga to jednak drobnego refaktoringu, gdyż nie mamy teraz dedykowanej funkcji która re-inicjalizuje obiekt `InterstitialAd`. W obrębie `initState` zastąp cały kod operujący na obiekcie `interstitial` wywołaniem funkcji `loadInterstitial`.

```dart
@override
void initState() {
  loadInterstitial();
  // ...
}

void loadInterstitial() {
  interstitial = InterstitialAd(
    adUnitId: InterstitialAd.testAdUnitId,
    targetingInfo: MobileAdTargetingInfo(),
  );
  interstitial.listener = (MobileAdEvent event) {
    if (event == MobileAdEvent.closed) {
      loadInterstitial();
    }
  };
  interstitial.load();
}
```

### Reklama nagradzająca (Rewarded video)

Brat bliźniak reklamy pełnoekranowej jeśli chodzi o formę prezentacji - zasłania cały ekran szukając atencji. Główna różnica polega na wyczuciu momentu w którym chcemy ją zaprezentować użytkownikowi. Bo o ile forma jest niemal identyczna, to intencja diametralnie się różni i należy być ostrożnym w jej używaniu. Jak nazwa sugeruje ma ona coś wspólnego z nagradzaniem użytkownika. Powstaje, więc **transakcja wiązana**: Ty dostajesz pieniądze za wyświetlenie, a użytkownik dostaje ... No właśnie, co?

To zależy. Od aplikacji. Od tego co jest w stanie zaoferować użytkownikowi w zamian za obejrzenie reklamy. Dodatkowe życie? Wirtualne złote monety? Czasowe odblokowanie pewnych funkcjonalności? Każda odpowiedź jest dobra. Musisz jedynie zaprojektować swój interfejs w taki sposób, aby użytkownik świadomie uruchomił reklamę tego typu i wiedział jaka czeka go nagroda za w pełni obejrzaną reklamę (z reguły trwają 30 sekund). SDK poinformuje Cię, czy użytkownik obejrzał reklamę w całości, czy też przerwał ją w trakcie (a wtedy nie dasz mu nagrody).

![reward_video.png](/assets/img/blog/admob/reward_video.png)

Mamy już doświadczenie z osadzaniem reklamy pełnoekranowej, tutaj implementacja będzie bardziej niż zbliżona.

```dart
@override
void initState() {
  loadRewardVideo();
  // ...
}

void loadRewardVideo() {
  RewardedVideoAd.instance.listener = (RewardedVideoAdEvent event, {String rewardType, int rewardAmount}) {
    if (event == RewardedVideoAdEvent.closed) {
      loadRewardVideo();
    }
  };
  RewardedVideoAd.instance.load(
    adUnitId: RewardedVideoAd.testAdUnitId,
    targetingInfo: MobileAdTargetingInfo(),
  );
}

void showRewardVideo() {
  RewardedVideoAd.instance.show();
}
```

Kod wygląda niemal identycznie jak w przypadku reklamy pełnoekranowej, a główna różnica polega na tym, że sami nie tworzymy instancji obiektu reklamowego. `RewardedVideoAd` udostępnia swoją instancję jako **singleton** poprzez `RewardedVideoAd.instance`. Jest to zabieg projektowy uniemożliwiający nam ładowania w jednym czasie więcej niż 1 reklamy nagradzającej. *Not a big deal*.

Drugą ważną, a nawet ważniejszą rzeczą niż singletonowy interfejs jest nietypowy **listener**. Przyjmuje on co prawda obiekt zdarzenia (nuda), ale pojawiają się też dwa nowe parametry:

- **rewardType** - rodzaj nagrody w postaci ciągu znaków, którą otrzyma użytkownik po obejrzeniu reklamy (np. *monety*).
- **rewardAmount** - wartość/kwota nagrody w postaci liczby całkowitej, którą nagrodzisz użytkownika (np. *10*).

Skąd pochodzą te wartości? Definiujesz je w momencie tworzenia nowej jednostki reklamowej z poziomu panelu administracyjnego AdMob. To od Ciebie zależy jakie wartości przyjmą i co z nimi zrobisz. W przykładzie korzystamy z `RewardedVideoAd.testAdUnitId`, który definiuje rodzaj nagrody jako *coins* o wartości równej *10* - czyli 10 wirtualnych monet za obejrzenie reklamy.

![admob_dashboard_reward_video.png](/assets/img/blog/admob/admob_dashboard_reward_video.png)

Reklama poprawnie wyświetla się na ekranie, pora do domu ...

![reward_video.gif](/assets/img/blog/admob/reward_video.gif)

... **nie tak szybko**. Pamiętasz definicję tej jednostki reklamowej? Transakcja wiązana, nagroda dla obu stron. Póki co Ty masz nagrodę, bo reklama została obejrzana, ale co z biednym użytkownikiem? Jego czas jest ~~bezwartościowy~~ bezcenny! Naprawmy nasz błąd, wprowadzając do gry ~~bezcenne~~ bezwartościowe monety, które można pozyskać na dwa sposoby:

1. Przejście poziomu w grze daje użytkownikowi **1** monetę.
2. Obejrzenie reklamy daje monet **10**. Taki **Pay-to-win** nam wyszedł, a raczej **Watch-to-win**.

Balans na bok, czas na wirtualną pseudowalutę! Na początku gry balans gracza to 0 monet.

```dart
class _AppState extends State<App> {
  int coins = 0;
  
  // ...
```

Przejście pojedynczego poziomu nagradza 1 monetą ...

```dart
void showInterstitial() async {
  setState(() {
    coins++;
    boxes.shuffle();
  });
  // ...
}
```

... a objerzenie reklamy rozbija bank w postaci 10 monet.

```dart
void loadRewardVideo() {
    RewardedVideoAd.instance.listener =
        (RewardedVideoAdEvent event, {String rewardType, int rewardAmount}) {
      // ...
      if (event == RewardedVideoAdEvent.completed) {
        setState(() {
          coins += rewardAmount;
        });
      }
    };
    // ...
  }
```

Pozostała jedynie kwestia wyświetlenia aktualnego salda monetowego, aby użytkownik mógł śledzić swój majątek i jego ciągły przyrost. Dodaj nowy widget do `Column` z prostym tekstem.

```dart
children: [
  Text("Coins: $coins"),
  // ...
]
```

Ostatnią rzeczą którą warto zrobić jest zmiana tekstu na przycisku wyświetlającym reklamę z **Show reward video** na **Get FREE coins**, lub coś równie wymownego. Nikt nie lubi reklam, ale każdy lubi darmowe fanty 💰.

## Reklamy produkcyjne

Wyświetlanie reklam testowych zadziałało bezbłędnie, ale w prawdziwej aplikacji zdecydowanie wolałbyś wyświetlać własne jednostki. W tym celu należy podmienić wystąpienia **xyzAd.testAdUnitId** na własne identyfikatory reklamowe wygenerowane w panelu AdMob.

1. Przejdź na stronę [https://admob.google.com/home/](https://admob.google.com/home/) i zaloguj się.
2. Z sekcji **Aplikacje** wybierz aplikację dla której chcesz utworzyć jednostki reklamowe.
3. Z bocznego menu przejdź do sekcji **Jednostki reklamowe** i kliknij przycisk **Zacznij teraz**

![adunit_1.png](/assets/img/blog/admob/adunit_1.png)

4. Wybierz typ jednostki którą chcesz utworzyć (**Zaawansowane reklamy natywne** nie są jeszcze wspierane we Flutterze)

![adunit_2.png](/assets/img/blog/admob/adunit_2.png)

5. Podaj nazwę jednostki, która będzie widoczna tylko dla Ciebie w panelu np. *[Android] testApp banner*
6. Skopiuj identyfikator reklamowy i użyj go przy inicjalizacji odpowiedniej jednostki reklamowej na danej platformie.

![adunit_3.png](/assets/img/blog/admob/adunit_3.png)

7. Powtórz czynność dla wszystkich platform/jednostek które chcesz wspierać w aplikacji.

> W przypadku gdy w konsoli pojawiają się błędy typu `failedToLoad` - odczekaj godzinę, dwie, aż nowo utworzona jednostka będzie poprawnie rozpoznawana przez systemy reklamodawcy. Cierpliwość popłaca.

## Podsumowanie

Masz teraz mocne i solidne podstawy - zarówno teoretyczne jak i praktyczne - dotyczące monetyzacji przez reklamy. Nie ma żadnych przeciwwskazań, abyś wybrał innego dostawcę reklam pod warunkiem, że istnieje stabilna paczka pod Fluttera. Pełny kod aplikacji reklamowej znajdziesz na [GitHubie](https://github.com/vintage/flutter_demo_ads).
