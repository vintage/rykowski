---
color: rgb(69,190,247)
title: Konfiguracja i rozpoczęcie przygody z Flutterem
date: "2019-06-12"
layout: "post"
slug: "flutter-getting-started"
excerpt: "Postawienie lokalnego środowiska developerskiego do pisania aplikacji we Flutterze. Instalacja niezbędnych paczek, edytora, aż do zbudowania Swojej pierwszej prostej apki."
---

Czym jest Flutter możesz dowiedzieć się z [poprzedniego wpisu](/blog/flutter-intro/), pora na praktykę. Konfiguracja środowiska,
instalacja niezbędnych zależności, podrasowanie edytora i ostatecznie zbudowanie pierwszej apki pod Androida lub iOSa (exclusive dla posiadaczy macOS). Zacznijmy kodzić!

![meme_lets_do_this.gif](/assets/img/blog/meme_lets_do_this.gif)

## Flutter SDK

Do rozpoczęcia naszej przygody z Flutterem potrzebujemy ... Fluttera. Da się zrobić! SDK dostępne jest do pobrania bezpośrednio z serwerów Google:

- [Wersja Linux](https://storage.googleapis.com/flutter_infra/releases/stable/linux/flutter_linux_1.17.5-stable.tar.xz)
- [Wersja macOS](https://storage.googleapis.com/flutter_infra/releases/stable/macos/flutter_macos_1.17.5-stable.zip)

Po ściągnięciu paczki należy ją rozpakować, a następnie przenieść katalog w sensowne miejsce. Jak odróżnić sensowne, od bezsensownego? Nie wiem, ale rzeczy tego typu osobiście trzymam w `/usr/local/opt/` dlatego Tobie też to polecam (chyba, że masz wyrobione własne preferencje, wtedy śmiało zrób tak, abyś czuł że masz nad tym kontrolę). Otwórz konsolę i przenieś świeżo rozpakowane archiwum do miejsca docelowego:

```shell
cd <KATALOG_GDZIE_ROZPAKOWAŁEŚ_ARCHIWUM>
mv flutter /usr/local/opt/
```

Gotowe. Pozostaje nam tylko powiadomić system, że w `/usr/local/opt/flutter/bin/` znajdują się pliki wykonywalne, które będziemy uruchamiać z poziomu konsoli. Chcemy w terminalu wpisać `flutter` i zobaczyć inny output niż "**Command not found**". Do osiągnięcia celu zaktualizujemy zmienną środowiskową `$PATH`:

```shell
echo export PATH='$PATH:/usr/local/opt/flutter/bin' >> ~/.bash_profile
```

Zrestartuj konsolę i voilà - po wpisaniu `flutter --version` otrzymasz informację o aktualnie zainstalowanej wersji Fluttera. **Yay!**

## Środowisko Android

Budowanie aplikacji na urządzenia z Androidem wymaga pobrania i zainstalowania [Android Studio](https://developer.android.com/studio). Gdy zainstalujesz już program, uruchom go i przejdź przez sugerowany proces pobierający Android SDK wraz z platform-tools i build-tools (narzędzia wykorzystywane m.in. do budowania plików ***.apk**).

Jako że główne narzędzie masz już zainstalowane, pozostało przygotować urządzenie na którym będziesz testował aplikację. Generalnie opcje są dwie - telefon, albo emulator. Oba rozwiązania są dobre, jednak czasem konieczne jest przetestowanie na fizycznym urządzeniu - bo jak sprawdzisz, że emulator wibruje? Ograniczenia technologiczne :)

### Konfiguracja urządzenia

1. Weź telefon do ręki.
2. Przejdź do ustawień i aktywuj opcje programistyczne **Ustawienia > Informacje o telefonie > Numer kompilacji/wersji**.  Uderz kilkanaście razy w tą opcję póki nie dostaniesz informacji o tym, że zostałeś programistą. Jeśli monit o byciu programistą się nie pojawi - spróbuj uderzać palcem w inną opcję, lub sprawdź w internecie jak dokładnie wygląda to na Twoim urządzeniu.
3. W ustawieniach odblokowana została nowa sekcja **Opcje programistyczne**. Przejdź do niej i włącz opcję **Debugowanie USB** oraz **Zezwalaj na instalację USB** (nie we wszystkich telefonach jest dostępna).
4. Podłącz kablem telefon do komputera.
5. Na telefonie potwierdź, że ufasz komputerowi do którego się właśnie podłączyłeś (bo ufasz prawda?).

### Konfiguracja emulatora

1. Uruchom **Android Studio** i z menu wybierz **Tools > AVD Manager**.
2. Kliknij w przycisk na dole ekranu **Create virtual device**.
3. W **Category** zaznacz **Phone**, wybierz dowolne urządzenie i kliknij w **Next**.
4. Wybierz wersję Androida do zainstalowana na emulatorze (polecam jedną z nowszych). Jeśli nie ściągałeś jeszcze żadnej to przy obrazach dostępny jest przycisk **Download**, który pobierze potrzebne pliki.
5. Kliknij w **Next**, a następnie **Finish**.
6. Wróć do **Tools > AVD Manager** i kliknij w ikonę **▶️** przy nowo utworzonym emulatorze.

Zwieńczeniem procesu - bez znaczenia czy konfigurujesz urządzenie czy emulator - jest wpisanie z poziomu konsoli `flutter devices` i upewnienie się że zwrócona lista nie jest pusta. Powinieneś otrzymać wynik zbliżony do:

```shell
$ flutter devices
1 connected device:

Android SDK built for x86 • emulator-5554 • android-x86 • Android 9 (API 28) (emulator)
```

## Środowisko iOS*

*Wyłącznie dla posiadaczy macOS - nie jest możliwe budowanie aplikacji iOS na innym systemie operacyjnym niż ten od Apple.*

Jeśli jesteś zainteresowany budowaniem aplikacji pod iOS, potrzebujesz narzędzia [Xcode](https://itunes.apple.com/us/app/xcode/id497799835). Po poprawnej instalacji (może chwilę zająć) wykonaj dodatkowo poniższe polecenia:

```shell
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -license
```

Odpowiadają one za konfigurację *CLI* oraz akceptację umowy licencyjnej (bez jej czytania, **Wow**!).

### Konfiguracja emulatora

1. Z konsoli wpisz `open -a Simulator`

W przypadku gdy chcesz sprecyzować jakie urządzenie ma zostać uruchomione (np. wolisz iPhone SE niż iPhone X):

1. Uruchom **Xcode**.
2. Utwórz nowy projekt przez **File > New > Project** (*⇧⌘N*).
3. Jako szablonu użyj **Single View App**.
4. Patrz obrazek: ![xcode_start_simulator.png](/assets/img/blog/xcode_start_simulator.png)

*Upewnij się, że opcja* **Debug > Slow animations** *jest wyłączona. Lubi czasem spłatać figla i się włączyć po cichu.*

## Punkt kontrolny

Czas się zatrzymać i zweryfikować, że wszystko zostało poprawnie zainstalowane i skonfigurowane. Flutter posiada do tego wbudowane polecenie, które upewni się że wszystko jest w jak najlepszym porządku.

```shell
$ flutter doctor

Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.5.4-hotfix.2, on Mac OS X 10.14.5 18F132, locale pl-PL)
[✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
[✓] iOS toolchain - develop for iOS devices (Xcode 10.2.1)
[✓] Android Studio (version 3.4)
[✓] VS Code (version 1.35.0)
[✓] Connected device (1 available)

• No issues found!
```

Wypis zawiera informację o:
- Flutter - w jakiej wersji zainstalowany jest sam framework
- Android toolchain - narzędzia do budowania aplikacji pod Androida
- iOS toolchain - narzędzia do budowania aplikacji pod iOSa (dostępne wyłącznie na macOS)
- Android Studio - IDE do tworzenia aplikacji
- VS Code - nie-IDE (edytor tekstu) do tworzenia aplikacji
- Connected devices - suma podłączonych urządzeń+uruchomionych emulatorów

Nie przejmuj się jeśli przy części opcji masz krzyżyk (chyba, że przy wszystkich) - możesz przykładowo nie chcieć instalować **VS Code**, czy też na początku chcesz pisać aplikację wyłącznie pod Androida.

## Uruchomienie aplikacji

Najłatwiejszym sposobem na uruchomienie czystej aplikacji we Flutterze jest utworzenie nowego projektu i jego odpalenie.

![meme_dont_say.jpg](/assets/img/blog/meme_dont_say.jpg)

Także to też zrobimy. Wsiadaj do konsoli i wpisuj razem ze mną:

```shell
flutter create my_app
cd my_app
flutter run
```

Twoim oczom powinna ukazać się następująca aplikacja startowa:

![flutter_first_app.png](/assets/img/blog/flutter_first_app.png)

Jest to typowy *counter* w którym po kliknięciu przycisku, licznik się zwiększa i tak bez końca. Teraz masz już 100% pewność, że Flutter gra i buczy i pozostaje Ci jedynie *pure development*, czyli to czym chcesz się zająć na poważnie. Ze swojej strony polecam oficjalny tutorial na [flutter.dev](https://flutter.dev/docs/reference/tutorials), który krok po kroku przedstawia najważniejsze składowe frameworka. 

![meme_gratz.gif](/assets/img/blog/meme_gratz.gif)
