# Instrukcja obsługi narzędzia do zastępowania tekstu w języku Esperanto znakami kanji i adnotacjami Ruby

## Spis treści
1. Wprowadzenie
2. Strona główna - zastępowanie tekstu w języku Esperanto
3. Strona dodatkowa - generowanie plików JSON do zastępowania
4. Formaty wyjściowe
5. Zaawansowane funkcje i konfiguracja
6. Przykłady zastosowania
7. Rozwiązywanie problemów

## 1. Wprowadzenie

Witaj w instrukcji obsługi aplikacji do zastępowania tekstu w języku Esperanto znakami kanji (汉字) i adnotacjami Ruby! To narzędzie umożliwia przekształcanie tekstów w języku Esperanto poprzez:

- Zastępowanie rdzeni słów esperanckich znakami kanji lub polskimi tłumaczeniami
- Dodawanie adnotacji Ruby (małych napisów nad tekstem, objaśniających znaczenie)
- Formatowanie tekstu w różnych stylach HTML
- Dostosowywanie reguł zastępowania do własnych potrzeb

Aplikacja składa się z dwóch głównych części:
- **Strona główna** - służąca do bezpośredniego zastępowania tekstów
- **Strona dodatkowa** - umożliwiająca tworzenie własnych plików JSON z regułami zastępowania

## 2. Strona główna - zastępowanie tekstu w języku Esperanto

### 2.1. Przygotowanie pliku JSON z regułami zastępowania

Pierwszym krokiem jest wybór pliku JSON zawierającego reguły zastępowania:

1. W sekcji "Jak postępować z plikiem JSON?" wybierz jedną z opcji:
   - **Użyj domyślnego pliku JSON** - aplikacja użyje wbudowanego pliku z regułami
   - **Prześlij plik** - możesz przesłać własny plik JSON z regułami zastępowania

2. Jeśli potrzebujesz pobrać przykładowy plik JSON, rozwiń sekcję "Pobierz przykładowy plik JSON (do zastępowania)" i kliknij przycisk "Pobierz przykładowy plik JSON".

### 2.2. Wprowadzanie tekstu w języku Esperanto

Masz dwie możliwości wprowadzenia tekstu do przetworzenia:

1. **Wpisywanie ręczne** - wybierz opcję "Wpisz ręcznie" i wprowadź tekst w języku Esperanto w polu tekstowym
2. **Przesyłanie pliku** - wybierz opcję "Prześlij plik" i prześlij plik tekstowy w formacie UTF-8

### 2.3. Wybór formatu wyjściowego

Wybierz jeden z dostępnych formatów wyjściowych:

- **Format HTML z adnotacjami Ruby i dostosowaniem rozmiaru** - tekst z adnotacjami Ruby i automatycznym dopasowaniem rozmiaru adnotacji
- **Format HTML z adnotacjami Ruby, dostosowaniem rozmiaru i zastępowaniem znaków kanji** - zamiana miejscami tekstu esperanckiego z kanji/tłumaczeniem
- **Format HTML** - podstawowy format HTML z adnotacjami Ruby
- **Format HTML z zastępowaniem znaków kanji** - podstawowy format z zamianą miejscami
- **Format z nawiasami** - tekst esperancki z tłumaczeniem w nawiasach
- **Format z nawiasami i zastępowaniem kanji** - tłumaczenie z tekstem esperanckim w nawiasach
- **Zachowaj tylko zastąpiony tekst** - wyświetla tylko tłumaczenie bez tekstu oryginalnego

### 2.4. Specjalne znaczniki tekstu

W tekście źródłowym możesz używać specjalnych znaczników:

- **%tekst%** - tekst umieszczony między znakami % nie zostanie zastąpiony i pozostanie niezmieniony w wyniku końcowym
- **@tekst@** - tekst umieszczony między znakami @ zostanie zastąpiony lokalnie (tylko w obrębie tego fragmentu)

### 2.5. Wybór formy wyświetlania znaków esperanckich

Wybierz sposób wyświetlania charakterystycznych dla Esperanto znaków z akcentem:

- **Znak akcentu nad literą (ĉ → c + ˆ)** - klasyczna notacja z akcentem nad literą
- **Format x (ĉ → cx)** - notacja z użyciem litery x po spółgłosce
- **Format ^ (ĉ → c^)** - notacja z użyciem znaku daszka po spółgłosce

### 2.6. Przetwarzanie tekstu

Po wprowadzeniu tekstu i wybraniu wszystkich opcji, kliknij przycisk "Wyślij", aby rozpocząć przetwarzanie. Po zakończeniu przetwarzania:

1. Zobaczysz podgląd wyniku (w zależności od wybranego formatu)
2. Możesz pobrać wynik, klikając przycisk "Pobierz wynik"

## 3. Strona dodatkowa - generowanie plików JSON do zastępowania

Strona dodatkowa pozwala na tworzenie własnych plików JSON z regułami zastępowania. To zaawansowana funkcja, pozwalająca na pełną kontrolę nad sposobem zastępowania tekstu.

### 3.1. Przygotowanie pliku CSV

Pierwszym krokiem jest wybór pliku CSV zawierającego rdzenie esperanckie i ich tłumaczenia:

1. Wybierz opcję "Prześlij własny plik" lub "Użyj pliku domyślnego"
2. W przypadku przesyłania własnego pliku, upewnij się, że ma on format CSV z kodowaniem UTF-8, zawierający dwie kolumny:
   - Pierwsza kolumna: rdzeń esperancki
   - Druga kolumna: polskie tłumaczenie lub znak kanji

Możesz pobrać przykładowe pliki CSV z różnymi tłumaczeniami, rozwijając sekcję "Lista plików przykładowych".

### 3.2. Przygotowanie plików JSON

Następnie potrzebujesz wybrać lub przesłać dwa pliki JSON:

1. **Plik JSON definiujący sposób dzielenia rdzeni esperanckich**:
   - Określa, jak dzielić słowa esperanckie na rdzenie
   - Definiuje, kiedy wstawiać formy pochodne (np. formy czasownika)

2. **Plik JSON definiujący tekst po zastąpieniu**:
   - Pozwala na przypisywanie znaków kanji lub specjalnych formatów do konkretnych słów
   - Jest opcjonalny, gdy wystarczy podstawowy plik CSV i plik z dzieleniem rdzeni

### 3.3. Zaawansowane ustawienia

Możesz ustawić opcje przetwarzania równoległego:

1. Zaznacz opcję "Użyj przetwarzania równoległego", jeśli chcesz przyspieszyć generowanie pliku JSON
2. Ustaw liczbę procesów równoległych (2-6, zalecane 4-5 dla typowego komputera)

### 3.4. Tworzenie pliku JSON

Po skonfigurowaniu wszystkich opcji:

1. Kliknij przycisk "Utwórz plik JSON do zastępowania"
2. Poczekaj na zakończenie procesu generowania (może potrwać kilka minut dla dużych plików)
3. Po zakończeniu, kliknij przycisk "Pobierz ostateczną listę zastępowania", aby pobrać wygenerowany plik JSON

Ten plik można następnie wykorzystać na stronie głównej, wybierając opcję "Prześlij plik" w sekcji dotyczącej pliku JSON.

## 4. Formaty wyjściowe

Aplikacja oferuje różne formaty wyjściowe, które determinują sposób prezentacji zastąpionego tekstu:

### 4.1. Formaty HTML z adnotacjami Ruby

Format Ruby to sposób prezentacji tekstu z małymi adnotacjami nad nim, powszechnie używany w językach wschodnioazjatyckich. W tej aplikacji:

- **Format HTML z adnotacjami Ruby i dostosowaniem rozmiaru** - automatycznie dopasowuje rozmiar adnotacji do długości tekstu podstawowego, dodaje stylizację CSS
- **Format HTML z adnotacjami Ruby, dostosowaniem rozmiaru i zastępowaniem znaków kanji** - jak powyżej, ale z zamianą miejscami tekstu esperanckiego i tłumaczenia
- **Format HTML** - podstawowy format HTML z adnotacjami Ruby, bez dodatkowego dostosowania rozmiaru
- **Format HTML z zastępowaniem znaków kanji** - podstawowy format HTML z zamianą miejscami tekstu esperanckiego i tłumaczenia

Przykład kodu HTML z adnotacjami Ruby:
```html
<ruby>esperanto<rt>tłumaczenie</rt></ruby>
```

### 4.2. Formaty z nawiasami

Prostsze formaty, które nie wymagają HTML, używające nawiasów do oznaczenia tłumaczenia:

- **Format z nawiasami** - tekst esperancki z tłumaczeniem w nawiasach, np. `esperanto(tłumaczenie)`
- **Format z nawiasami i zastępowaniem kanji** - tłumaczenie z tekstem esperanckim w nawiasach, np. `tłumaczenie(esperanto)`

### 4.3. Proste zastępowanie

Najprostszy format, który pokazuje tylko tłumaczenie, całkowicie zastępując oryginalny tekst:

- **Zachowaj tylko zastąpiony tekst** - wyświetla tylko tłumaczenie bez śladu tekstu oryginalnego

## 5. Zaawansowane funkcje i konfiguracja

### 5.1. Przetwarzanie równoległe

Aby przyspieszyć przetwarzanie dużych tekstów, aplikacja oferuje opcję przetwarzania równoległego:

1. Rozwiń sekcję "Zaawansowane ustawienia (przetwarzanie równoległe)"
2. Zaznacz opcję "Użyj przetwarzania równoległego"
3. Ustaw liczbę równoczesnych procesów (2-4, w zależności od możliwości komputera)

Ta opcja jest szczególnie przydatna przy dużych tekstach lub na komputerach wielordzeniowych.

### 5.2. Dostosowywanie plików JSON

Dla zaawansowanych użytkowników, istnieje możliwość szczegółowego dostosowania plików JSON:

1. **Dzielenie rdzeni słów esperanckich**:
   - Można określić, jak aplikacja ma dzielić słowa na rdzenie
   - Można ustawić specjalne reguły dla czasowników, przyrostków i przedrostków

2. **Niestandardowe tłumaczenia**:
   - Możliwość przypisania konkretnych tłumaczeń do określonych słów
   - Opcja wyłączenia zastępowania dla wybranych słów

### 5.3. Specjalne znaczniki w tekście

Aplikacja obsługuje dwa specjalne znaczniki w tekście:

- **%tekst%** - tekst, który ma pozostać niezmieniony (maksymalnie 50 znaków)
- **@tekst@** - tekst, który ma być przetworzony lokalnie (maksymalnie 18 znaków)

Te znaczniki są szczególnie przydatne, gdy chcesz mieć precyzyjną kontrolę nad tym, które części tekstu są zastępowane i w jaki sposób.

## 6. Przykłady zastosowania

### 6.1. Podstawowe zastępowanie tekstu

1. Wybierz "Użyj domyślnego pliku JSON"
2. Wybierz "Wpisz ręcznie" i wprowadź prosty tekst w języku Esperanto, np. `Mi amas legi librojn.`
3. Wybierz format "Format HTML z adnotacjami Ruby i dostosowaniem rozmiaru"
4. Wybierz "Znak akcentu nad literą" jako formę wyświetlania znaków
5. Kliknij "Wyślij"
6. Zobacz rezultat i pobierz plik HTML

### 6.2. Używanie znaczników specjalnych

Wprowadź tekst: `Mi %amas% legi @librojn@.`

- Słowo "amas" pozostanie niezmienione
- Słowo "librojn" zostanie zastąpione zgodnie z regułami lokalnymi

### 6.3. Tworzenie własnego pliku JSON

1. Przejdź do strony "Strona generująca plik JSON do zastępowania tekstu w Esperanto"
2. Prześlij własny plik CSV z rdzeniami esperanckimi i tłumaczeniami
3. Użyj domyślnych plików JSON dla dzielenia rdzeni i tekstu po zastąpieniu
4. Włącz przetwarzanie równoległe
5. Kliknij "Utwórz plik JSON do zastępowania"
6. Pobierz wygenerowany plik
7. Wróć do strony głównej i użyj tego pliku do zastępowania tekstu

## 7. Rozwiązywanie problemów

### 7.1. Tekst nie jest zastępowany poprawnie

- Upewnij się, że używasz poprawnego pliku JSON z regułami zastępowania
- Sprawdź, czy tekst nie zawiera niestandardowych znaków, które mogą powodować problemy
- Jeśli używasz znaczników % lub @, upewnij się, że są one poprawnie sparowane

### 7.2. Błędy podczas przesyłania plików

- Upewnij się, że pliki CSV są zapisane w formacie UTF-8
- Sprawdź, czy pliki JSON mają poprawną strukturę
- Upewnij się, że pliki nie są zbyt duże (do 50 MB dla plików JSON)

### 7.3. Problemy z formatowaniem HTML

- Jeśli adnotacje Ruby nie wyświetlają się poprawnie, wybierz inny format wyjściowy
- Upewnij się, że przeglądarka obsługuje tagi <ruby> HTML
- W przypadku bardzo długich adnotacji, wybierz format z dostosowaniem rozmiaru

---

Dziękujemy za korzystanie z naszej aplikacji do zastępowania tekstu w języku Esperanto! Mamy nadzieję, że to narzędzie będzie pomocne w Twoich projektach związanych z językiem Esperanto i japońską lub chińską adnotacją. W przypadku pytań lub problemów, zapraszamy do kontaktu poprzez odnośniki GitHub podane na końcu strony głównej aplikacji.