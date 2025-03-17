# Przewodnik techniczny: Struktura i mechanizmy aplikacji do zastępowania tekstów w języku Esperanto

## Spis treści
1. Przegląd architektury aplikacji
2. Struktura i przepływ danych
3. Moduły funkcjonalne i ich zależności
4. Algorytmy zastępowania tekstu
5. Mechanizm placeholderów i przetwarzanie wieloetapowe
6. Obsługa formatów HTML i adnotacji Ruby
7. Implementacja przetwarzania równoległego
8. Generowanie plików JSON z regułami zastępowania
9. Analiza wydajności i optymalizacje

## 1. Przegląd architektury aplikacji

Aplikacja do zastępowania tekstów esperanckich jest zbudowana na platformie Streamlit i składa się z czterech głównych komponentów:

1. **main.py** - Główny interfejs użytkownika i logika aplikacji
2. **Strona generująca plik JSON...** - Moduł do tworzenia plików JSON z regułami zastępowania
3. **esp_text_replacement_module.py** - Biblioteka funkcji do przetwarzania tekstu esperanckiego
4. **esp_replacement_json_make_module.py** - Biblioteka funkcji do generowania plików JSON

Ogólny przepływ pracy aplikacji można przedstawić następująco:

```
[Interfejs użytkownika (Streamlit)] <- wejście -> [Przetwarzanie tekstu] -> [Zastępowanie tekstu] -> [Formatowanie wyjściowe] -> [Wyświetlanie/Pobieranie]
```

Dane przepływają przez system w następujący sposób:
- Tekst w języku Esperanto jest wprowadzany przez użytkownika lub wczytywany z pliku
- Zasady zastępowania są wczytywane z pliku JSON
- Tekst jest przetwarzany przy użyciu algorytmów zastępowania
- Wynik jest formatowany zgodnie z wybranym formatem (HTML, adnotacje Ruby, nawiasy)
- Sformatowany tekst jest wyświetlany użytkownikowi i dostępny do pobrania

Aplikacja wykorzystuje podejście modułowe, gdzie każdy komponent jest odpowiedzialny za konkretny aspekt funkcjonalności.

## 2. Struktura i przepływ danych

### 2.1. Główne struktury danych

Aplikacja operuje na kilku kluczowych strukturach danych:

1. **Listy zastępowania (replacements lists)** - Trójki (old, new, placeholder) definiujące reguły zastępowania tekstu:
   ```python
   replacements_final_list = [("esperanto", "polski", "placeholder1"), ...]
   ```

2. **Słowniki placeholderów** - Mapują oryginalne teksty na tymczasowe znaczniki:
   ```python
   valid_replacements = {"placeholder1": "nowy_tekst", ...}
   ```

3. **Listy części do pominięcia/lokalnego zastępowania** - Wykorzystywane do specjalnego traktowania fragmentów tekstu:
   ```python
   replacements_list_for_intact_parts = [("%tekst%", "placeholder"), ...]
   ```

### 2.2. Przepływ danych w głównym module

W pliku `main.py` dane przepływają następująco:

1. **Wczytywanie pliku JSON z regułami zastępowania**:
   ```python
   @st.cache_data
   def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
       with open(json_path, 'r', encoding='utf-8') as f:
           data = json.load(f)
       # Ekstrakcja trzech typów list zastępowania
       replacements_final_list = data.get("全域替换用のリスト(列表)型配列(replacements_final_list)", [])
       replacements_list_for_localized_string = data.get("局部文字替换用のリスト(列表)型配列(replacements_list_for_localized_string)", [])
       replacements_list_for_2char = data.get("二文字词根替换用のリスト(列表)型配列(replacements_list_for_2char)", [])
       return (replacements_final_list, replacements_list_for_localized_string, replacements_list_for_2char)
   ```

2. **Wczytywanie placeholderów**:
   ```python
   placeholders_for_skipping_replacements = import_placeholders('./Appの运行に使用する各类文件/占位符(placeholders)_%1854%-%4934%_文字列替换skip用.txt')
   placeholders_for_localized_replacement = import_placeholders('./Appの运行に使用する各类文件/占位符(placeholders)_@5134@-@9728@_局部文字列替换结果捕捉用.txt')
   ```

3. **Przetwarzanie tekstu**:
   ```python
   processed_text = orchestrate_comprehensive_esperanto_text_replacement(
       text=text0,
       placeholders_for_skipping_replacements=placeholders_for_skipping_replacements,
       replacements_list_for_localized_string=replacements_list_for_localized_string,
       placeholders_for_localized_replacement=placeholders_for_localized_replacement,
       replacements_final_list=replacements_final_list,
       replacements_list_for_2char=replacements_list_for_2char,
       format_type=format_type
   )
   ```

## 3. Moduły funkcjonalne i ich zależności

Aplikacja jest zorganizowana w moduły funkcjonalne, które są ze sobą powiązane:

### 3.1. Moduł przetwarzania tekstu (`esp_text_replacement_module.py`)

Ten moduł zawiera podstawowe funkcje do przetwarzania tekstu esperanckiego:

- **Konwersja znaków** - Funkcje do konwersji między różnymi formatami znaków esperanckich (x-system, system daszka, znaki z akcentem)
- **Funkcje zastępowania** - `safe_replace()`, `orchestrate_comprehensive_esperanto_text_replacement()`
- **Funkcje placeholderów** - `import_placeholders()`, `create_replacements_list_for_intact_parts()`
- **Przetwarzanie równoległe** - `parallel_process()`, `process_segment()`

### 3.2. Moduł generowania JSON (`esp_replacement_json_make_module.py`)

Ten moduł zawiera funkcje do tworzenia plików JSON z regułami zastępowania:

- **Formatowanie wyjściowe** - `output_format()`, `insert_br_at_half_width()`
- **Przetwarzanie równoległe przy generowaniu** - `parallel_build_pre_replacements_dict()`
- **Manipulacje HTML i adnotacjami Ruby** - `capitalize_ruby_and_rt()`, `remove_redundant_ruby_if_identical()`

### 3.3. Zależności między modułami

Zależności między modułami można przedstawić następująco:

```
main.py
  ├── esp_text_replacement_module.py
  │     └── (podstawowe funkcje przetwarzania tekstu)
  └── Strona generująca plik JSON...
        ├── esp_text_replacement_module.py
        └── esp_replacement_json_make_module.py
             └── (funkcje do generowania plików JSON)
```

Główny plik aplikacji (`main.py`) importuje funkcje z modułu `esp_text_replacement_module.py` do przetwarzania tekstu. Dodatkowa strona do generowania plików JSON importuje funkcje zarówno z `esp_text_replacement_module.py`, jak i z `esp_replacement_json_make_module.py`.

## 4. Algorytmy zastępowania tekstu

Główny algorytm zastępowania tekstu jest zaimplementowany w funkcji `orchestrate_comprehensive_esperanto_text_replacement()`. Proces przebiega w następujących etapach:

### 4.1. Przygotowanie tekstu

```python
# 1, 2) Normalizacja spacji + konwersja znaków esperanckich
text = unify_halfwidth_spaces(text)
text = convert_to_circumflex(text)
```

Najpierw tekst jest przygotowywany przez normalizację spacji i konwersję znaków esperanckich do standardowego formatu z akcentem (np. `cx` → `ĉ`).

### 4.2. Ochrona fragmentów tekstu

```python
# 3) Tymczasowa zamiana fragmentów %...% (do pominięcia)
replacements_list_for_intact_parts = create_replacements_list_for_intact_parts(text, placeholders_for_skipping_replacements)
# Sortowanie według długości (od najdłuższego)
sorted_replacements_list_for_intact_parts = sorted(replacements_list_for_intact_parts, key=lambda x: len(x[0]), reverse=True)
for original, place_holder_ in sorted_replacements_list_for_intact_parts:
    text = text.replace(original, place_holder_)
```

Fragmenty tekstu otoczone znakami `%` są chronione przed zastępowaniem przez tymczasową zamianę ich na unikalne placeholdery.

### 4.3. Lokalne zastępowanie

```python
# 4) Lokalne zastępowanie fragmentów @...@
tmp_replacements_list_for_localized_string_2 = create_replacements_list_for_localized_replacement(
    text, placeholders_for_localized_replacement, replacements_list_for_localized_string
)
sorted_replacements_list_for_localized_string = sorted(tmp_replacements_list_for_localized_string_2, key=lambda x: len(x[0]), reverse=True)
for original, place_holder_, replaced_original in sorted_replacements_list_for_localized_string:
    text = text.replace(original, place_holder_)
```

Fragmenty tekstu otoczone znakami `@` są zastępowane lokalnie, a następnie również zamieniane na placeholdery.

### 4.4. Główne zastępowanie globalne

```python
# 5) Zastępowanie globalne (old, new, placeholder)
valid_replacements = {}
for old, new, placeholder in replacements_final_list:
    if old in text:
        text = text.replace(old, placeholder)
        valid_replacements[placeholder] = new
```

Główny algorytm zastępowania działa w dwóch krokach:
1. Zamiana oryginalnego tekstu na placeholdery
2. Zamiana placeholderów na docelowy tekst

Ten dwuetapowy proces zapobiega problemom z częściowym zastępowaniem, gdy jeden fragment tekstu mógłby być zastąpiony więcej niż raz.

### 4.5. Zastępowanie dwuznakowych rdzeni

```python
# 6) Zastępowanie dwuznakowych rdzeni (2 razy)
valid_replacements_for_2char_roots = {}
for old, new, placeholder in replacements_list_for_2char:
    if old in text:
        text = text.replace(old, placeholder)
        valid_replacements_for_2char_roots[placeholder] = new
```

Dwuznakowe rdzenie wyrazów są zastępowane w podobny sposób, ale w osobnym kroku, aby uniknąć konfliktów z głównym zastępowaniem.

### 4.6. Przywracanie zastąpionych fragmentów

```python
# 7) Przywracanie placeholderów do ostatecznego tekstu
for place_holder_second, new in reversed(valid_replacements_for_2char_roots_2.items()):
    text = text.replace(place_holder_second, new)
for placeholder, new in reversed(valid_replacements_for_2char_roots.items()):
    text = text.replace(placeholder, new)
for placeholder, new in valid_replacements.items():
    text = text.replace(placeholder, new)
# Przywracanie lokalnych (@) i pominiętych (%) fragmentów
for original, place_holder_, replaced_original in sorted_replacements_list_for_localized_string:
    text = text.replace(place_holder_, replaced_original.replace("@",""))
for original, place_holder_ in sorted_replacements_list_for_intact_parts:
    text = text.replace(place_holder_, original.replace("%",""))
```

Na końcu, wszystkie placeholdery są zastępowane odpowiednimi tekstami docelowymi, a specjalne fragmenty (otoczone `%` lub `@`) są przywracane do oryginalnej lub lokalnie zastąpionej formy.

## 5. Mechanizm placeholderów i przetwarzanie wieloetapowe

Jednym z kluczowych mechanizmów w aplikacji jest system placeholderów, który umożliwia bezpieczne zastępowanie tekstu.

### 5.1. Rola placeholderów

Placeholdery to unikalne ciągi znaków, które służą jako tymczasowe znaczniki w procesie zastępowania. Są używane, aby:

1. Uniknąć problemu z częściowym zastępowaniem (gdy część już zastąpionego tekstu mogłaby zostać zastąpiona ponownie)
2. Umożliwić ochronę fragmentów tekstu przed zastępowaniem (fragmenty otoczone `%`)
3. Umożliwić lokalne zastępowanie fragmentów tekstu (fragmenty otoczone `@`)

### 5.2. Wczytywanie placeholderów

```python
def import_placeholders(filename: str) -> List[str]:
    with open(filename, 'r') as file:
        placeholders = [line.strip() for line in file if line.strip()]
    return placeholders
```

Placeholdery są wczytywane z zewnętrznych plików tekstowych, co zapewnia ich unikalność i separację od normalnego tekstu.

### 5.3. Proces dwuetapowego zastępowania

```python
def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
    valid_replacements = {}
    # Najpierw old → placeholder
    for old, new, placeholder in replacements:
        if old in text:
            text = text.replace(old, placeholder)
            valid_replacements[placeholder] = new
    # Następnie placeholder → new
    for placeholder, new in valid_replacements.items():
        text = text.replace(placeholder, new)
    return text
```

Funkcja `safe_replace()` implementuje dwuetapowy proces zastępowania:
1. Zamiana oryginalnego tekstu na placeholdery
2. Zamiana placeholderów na docelowy tekst

To podejście zapobiega problemom z "nakładającymi się" zastąpieniami i zapewnia, że każdy fragment tekstu jest zastępowany tylko raz.

## 6. Obsługa formatów HTML i adnotacji Ruby

Aplikacja obsługuje różne formaty wyjściowe, w tym formaty HTML z adnotacjami Ruby, które są używane do wyświetlania małych adnotacji nad tekstem.

### 6.1. Formatowanie wyjściowe

```python
def output_format(main_text, ruby_content, format_type, char_widths_dict):
    if format_type == 'HTML格式_Ruby文字_大小调整':
        width_ruby = measure_text_width_Arial16(ruby_content, char_widths_dict)
        width_main = measure_text_width_Arial16(main_text, char_widths_dict)
        ratio_1 = width_ruby / width_main
        if ratio_1 > 6:
            return f'<ruby>{main_text}<rt class="XXXS_S">{insert_br_at_third_width(ruby_content, char_widths_dict)}</rt></ruby>'
        # [inne warunki dla różnych proporcji]
    # [inne formaty wyjściowe]
```

Funkcja `output_format()` w module `esp_replacement_json_make_module.py` określa, jak formatować zastąpiony tekst. W przypadku adnotacji Ruby, funkcja analizuje proporcję szerokości tekstu głównego i adnotacji, aby wybrać odpowiednią klasę CSS dla adnotacji.

### 6.2. Pomiar szerokości tekstu

```python
def measure_text_width_Arial16(text, char_widths_dict: Dict[str, int]) -> int:
    total_width = 0
    for ch in text:
        char_width = char_widths_dict.get(ch, 8)
        total_width += char_width
    return total_width
```

Aplikacja używa słownika szerokości znaków, aby precyzyjnie określić szerokość tekstu, co jest kluczowe dla odpowiedniego formatowania adnotacji Ruby.

### 6.3. Automatyczne dzielenie długich adnotacji

```python
def insert_br_at_half_width(text, char_widths_dict: Dict[str, int]) -> str:
    total_width = measure_text_width_Arial16(text, char_widths_dict)
    half_width = total_width / 2
    current_width = 0
    insert_index = None
    for i, ch in enumerate(text):
        char_width = char_widths_dict.get(ch, 8)
        current_width += char_width
        if current_width >= half_width:
            insert_index = i + 1
            break
    if insert_index is not None:
        result = text[:insert_index] + "<br>" + text[insert_index:]
    else:
        result = text
    return result
```

Dla długich adnotacji, aplikacja automatycznie wstawia znaczniki `<br>`, aby podzielić adnotację na wiersze. Funkcja `insert_br_at_half_width()` wstawia pojedynczy znacznik `<br>` w połowie szerokości tekstu, a funkcja `insert_br_at_third_width()` wstawia dwa znaczniki, dzieląc tekst na trzy części.

### 6.4. Dodawanie nagłówka i stopki HTML

```python
def apply_ruby_html_header_and_footer(processed_text: str, format_type: str) -> str:
    if format_type in ('HTML格式_Ruby文字_大小调整','HTML格式_Ruby文字_大小调整_汉字替换'):
        ruby_style_head = """<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8">
    <!-- [Style CSS for Ruby] -->
  </head>
  <body>
  <p class="text-M_M">
"""
        ruby_style_tail = "</p></body></html>"
    # [inne formaty]
    return ruby_style_head + processed_text + ruby_style_tail
```

Dla formatów HTML, aplikacja dodaje odpowiedni nagłówek i stopkę HTML, w tym style CSS dla adnotacji Ruby.

## 7. Implementacja przetwarzania równoległego

Aplikacja wykorzystuje moduł `multiprocessing` do równoległego przetwarzania dużych tekstów, co znacznie przyspiesza operacje zastępowania.

### 7.1. Przetwarzanie równoległe tekstu

```python
def parallel_process(
    text: str,
    num_processes: int,
    # [inne parametry]
) -> str:
    if num_processes <= 1:
        # Pojedynczy proces
        return orchestrate_comprehensive_esperanto_text_replacement(
            text,
            # [parametry]
        )
    
    # Podział tekstu na linie
    lines = re.findall(r'.*?\n|.+$', text)
    num_lines = len(lines)
    if num_lines <= 1:
        # Pojedynczy proces dla krótkich tekstów
        return orchestrate_comprehensive_esperanto_text_replacement(
            text,
            # [parametry]
        )
    
    # Podział na zakresy dla procesów
    lines_per_process = max(num_lines // num_processes, 1)
    ranges = [(i * lines_per_process, (i + 1) * lines_per_process) for i in range(num_processes)]
    # Ostatni proces otrzymuje wszystkie pozostałe linie
    ranges[-1] = (ranges[-1][0], num_lines)
    
    # Równoległe przetwarzanie
    with multiprocessing.Pool(processes=num_processes) as pool:
        results = pool.starmap(
            process_segment,
            [
                (
                    lines[start:end],
                    # [parametry]
                )
                for (start, end) in ranges
            ]
        )
    
    # Połączenie wyników
    return ''.join(results)
```

Funkcja `parallel_process()` dzieli tekst wejściowy na fragmenty (według linii) i przetwarza każdy fragment równolegle w oddzielnym procesie. Wyniki są następnie łączone w jeden sformatowany tekst.

### 7.2. Przetwarzanie segmentu tekstu

```python
def process_segment(
    lines: List[str],
    # [parametry]
) -> str:
    segment = ''.join(lines)
    result = orchestrate_comprehensive_esperanto_text_replacement(
        segment,
        # [parametry]
    )
    return result
```

Każdy segment tekstu (zbiór linii) jest przetwarzany przez tę samą funkcję `orchestrate_comprehensive_esperanto_text_replacement()`, ale w oddzielnym procesie.

## 8. Generowanie plików JSON z regułami zastępowania

Druga strona aplikacji (`Strona generująca plik JSON...`) umożliwia tworzenie plików JSON z regułami zastępowania.

### 8.1. Struktura pliku JSON

Plik JSON z regułami zastępowania zawiera trzy główne listy:

1. **replacements_final_list** - Lista trójek (old, new, placeholder) do globalnego zastępowania
2. **replacements_list_for_localized_string** - Lista trójek do lokalnego zastępowania (dla fragmentów `@...@`)
3. **replacements_list_for_2char** - Lista trójek do zastępowania dwuznakowych rdzeni

### 8.2. Tworzenie listy zastępowania

```python
# Tworzenie listy dla podstawowego zastępowania
temporary_replacements_dict = {}
with open("./Appの运行に使用する各类文件/世界语全部词根_约11137个_202501.txt", 'r', encoding='utf-8') as file:
    E_roots = file.readlines()
    for E_root in E_roots:
        E_root = E_root.strip()
        if not E_root.isdigit():
            temporary_replacements_dict[E_root] = [E_root, len(E_root)]

# Aktualizacja na podstawie danych CSV
for *, (E*root, hanzi_or_meaning) in CSV_data_imported.iterrows():
    if pd.notna(E_root) and pd.notna(hanzi_or_meaning) \
       and '#' not in E_root and (E_root != '') and (hanzi_or_meaning != ''):
        temporary_replacements_dict[E_root] = [
            output_format(E_root, hanzi_or_meaning, format_type, char_widths_dict),
            len(E_root)
        ]
```

Aplikacja tworzy słownik zastępowania na podstawie:
1. Listy wszystkich rdzeni esperanckich z pliku tekstowego
2. Danych z pliku CSV, który mapuje rdzenie esperanckie na znaki kanji lub tłumaczenia

### 8.3. Przetwarzanie niestandardowych ustawień

```python
if len(custom_stemming_setting_list) > 0:
    # Usunięcie wiersza opisu
    if len(custom_stemming_setting_list[0]) != 3:
        custom_stemming_setting_list.pop(0)
for i in custom_stemming_setting_list:
    if len(i)==3:
        try:
            esperanto_Word_before_replacement = i[0].replace('/', '')
            if i[1] == "dflt":
                replacement_priority_by_length = len(esperanto_Word_before_replacement)*10000
            elif i[1] in allowed_values:
                # Wykluczenie słowa z zastępowania
                pre_replacements_dict_3.pop(esperanto_Word_before_replacement, None)
                # [dalsza logika]
            # [dalsza logika dla innych przypadków]
        except:
            continue
```

Aplikacja przetwarza niestandardowe ustawienia z pliku JSON, który określa, jak dzielić słowa esperanckie na rdzenie i jak tworzyć formy pochodne.

### 8.4. Generowanie ostatecznego pliku JSON

```python
combined_data = {}
combined_data["全域替换用のリスト(列表)型配列(replacements_final_list)"] = replacements_final_list
combined_data["二文字词根替换用のリスト(列表)型配列(replacements_list_for_2char)"] = replacements_list_for_2char
combined_data["局部文字替换用のリスト(列表)型配列(replacements_list_for_localized_string)"] = replacements_list_for_localized_string
# Zapis do JSON
download_data = json.dumps(combined_data, ensure_ascii=False, indent=2)
```

Na końcu, trzy listy zastępowania są łączone w jeden słownik i zapisywane jako plik JSON.

## 9. Analiza wydajności i optymalizacje

Aplikacja zawiera kilka optymalizacji, aby zapewnić wydajne przetwarzanie dużych tekstów:

### 9.1. Buforowanie danych z JSON

```python
@st.cache_data
def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
    # [implementacja]
```

Dekorator `@st.cache_data` w Streamlit buforuje wyniki funkcji `load_replacements_lists()`, co oznacza, że pliki JSON są wczytywane tylko raz, nawet jeśli funkcja jest wywoływana wielokrotnie.

### 9.2. Sortowanie według długości

```python
sorted_replacements_list_for_intact_parts = sorted(replacements_list_for_intact_parts, key=lambda x: len(x[0]), reverse=True)
```

Aplikacja sortuje listy zastępowania według długości tekstu do zastąpienia (od najdłuższego do najkrótszego), co zapobiega problemom z częściowym zastępowaniem, gdy krótszy tekst jest zawarty w dłuższym.

### 9.3. Wykorzystanie wieloprocesowości

```python
if use_parallel:
    processed_text = parallel_process(
        text=text0,
        num_processes=num_processes,
        # [parametry]
    )
else:
    processed_text = orchestrate_comprehensive_esperanto_text_replacement(
        text=text0,
        # [parametry]
    )
```

Aplikacja oferuje opcję przetwarzania równoległego, co znacznie przyspiesza przetwarzanie dużych tekstów.

### 9.4. Dwuetapowe zastępowanie

```python
def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
    valid_replacements = {}
    # Najpierw old → placeholder
    for old, new, placeholder in replacements:
        if old in text:
            text = text.replace(old, placeholder)
            valid_replacements[placeholder] = new
    # Następnie placeholder → new
    for placeholder, new in valid_replacements.items():
        text = text.replace(placeholder, new)
    return text
```

Dwuetapowy proces zastępowania (old → placeholder → new) zapobiega problemom z nakładającymi się zastąpieniami i zapewnia, że każdy fragment tekstu jest zastępowany tylko raz, co jest bardziej wydajne niż wielokrotne przechodzenie przez tekst.

---

To kompleksowe wyjaśnienie architektury i mechanizmów aplikacji powinno pomóc średnio-zaawansowanym programistom zrozumieć, jak działa ta aplikacja i jak mogą ją dostosować do swoich potrzeb. Kolejne sekcje będą zawierać bardziej szczegółowe wyjaśnienia konkretnych aspektów aplikacji.