# Projekt IDM: Asystent Prawa o Ruchu Drogowym i Taryfikatora

**Autorzy:** Mateusz Chmurzyński, Michał Rusinek

## 1. Temat projektu

Projekt zakłada stworzenie asystenta, który pomaga użytkownikom sprawdzać przepisy ruchu drogowego oraz ustalać konsekwencje popełnionych wykroczeń, w tym wysokość mandatów i liczbę punktów karnych. System wykorzystuje podejście RAG (Retrieval-Augmented Generation) oraz lokalny model językowy (LLM), aby udzielać odpowiedzi na podstawie oficjalnych aktów prawnych.

## 2. Jaki problem rozwiązuje system?

Informacje potrzebne do oceny konkretnego wykroczenia są często rozproszone w kilku aktach prawnych. Przepisy określające zasady zachowania na drodze, wysokość grzywny i liczbę punktów karnych znajdują się w różnych dokumentach, dlatego znalezienie kompletnej odpowiedzi może wymagać samodzielnego przeanalizowania kilku źródeł.

Dodatkowym wyzwaniem jest sposób, w jaki kierowcy zadają pytania. Zamiast posługiwać się terminologią prawną, używają potocznych określeń, takich jak „jazda na zderzaku”, albo opisują konkretne sytuacje, np. wyprzedzanie na przejściu dla pieszych.

Asystent ma rozpoznawać intencję użytkownika, wyszukiwać odpowiednie fragmenty dokumentów i łączyć informacje z różnych źródeł w jednej odpowiedzi. Przykładowo, na pytanie „Jaka kara grozi za jazdę na zderzaku na autostradzie - mandat i punkty?” system powinien znaleźć zarówno przepisy dotyczące bezpiecznego odstępu, jak i informacje o konsekwencjach danego wykroczenia.

Odpowiedzi mają być oparte na dostarczonych dokumentach i zawierać odwołania do źródeł, tak aby użytkownik mógł samodzielnie zweryfikować przedstawione informacje.

## 3. Zakres danych

### Dokumenty źródłowe (RAG)

System korzysta z czterech oficjalnych aktów prawnych pobranych z systemu ISAP i zapisanych w formacie PDF w katalogu `data/raw/`:

1. Ustawa - Prawo o ruchu drogowym.
2. Rozporządzenie w sprawie wysokości grzywien nakładanych w drodze mandatów karnych (taryfikator mandatów).
3. Rozporządzenie w sprawie ewidencji kierujących naruszających przepisy ruchu drogowego (taryfikator punktów karnych).
4. Rozporządzenie w sprawie znaków i sygnałów drogowych.

### Zestaw ewaluacyjny (Golden Set)

Do oceny działania systemu przygotowano zbiór 150 pytań zapisany w pliku `eval/golden_set.jsonl`. Pytania obejmują różne rodzaje zapytań, od prostych pytań o przepisy po przypadki wymagające połączenia informacji z kilku dokumentów.

Zbiór podzielono na trzy kategorie:

- **`easy`** - proste pytania dotyczące przepisów i znaków drogowych, również sformułowane językiem potocznym.
- **`multi`** - pytania wymagające połączenia informacji z kilku źródeł, np. ustalenia zasad dotyczących danego wykroczenia, wysokości mandatu i liczby punktów karnych.
- **`out_of_scope`** - pytania wykraczające poza zakres dostępnych aktów prawnych, np. dotyczące akcyzy, mechaniki pojazdów, ubezpieczeń OC/AC czy mandatów za granicą. Ta kategoria pozwala sprawdzić, czy system potrafi rozpoznać pytania, na które nie powinien odpowiadać na podstawie dostępnej bazy wiedzy.

Golden set służy do porównywania odpowiedzi systemu i oceny, czy poprawnie wyszukuje informacje, łączy dane z różnych dokumentów oraz rozpoznaje pytania spoza swojego zakresu.

## 4. Zadanie agenta w etapie 3 (Tool Calling)

W trzecim etapie projektu (E3) system zostanie rozszerzony o narzędzia napisane w Pythonie. Pozwolą one wykonywać obliczenia i sprawdzać warunki, w których samo generowanie odpowiedzi przez model językowy mogłoby prowadzić do błędów.

Planowane narzędzia:

1. **`calculate_speeding_penalty`** - obliczanie konsekwencji przekroczenia prędkości na podstawie podanej prędkości i obowiązującego limitu. Narzędzie uwzględni odpowiednią stawkę mandatu, punkty karne, przesłanki recydywy oraz warunki związane z zatrzymaniem prawa jazdy na 3 miesiące.

2. **`calculate_safe_distance`** - obliczanie wymaganego odstępu od poprzedzającego pojazdu na autostradzie lub drodze ekspresowej oraz sprawdzanie szczególnych zasad dotyczących odstępu w tunelach.

3. **`check_driver_points_limit`** - sprawdzanie, czy liczba punktów karnych kierowcy przekracza właściwy limit, z uwzględnieniem stażu kierowcy i zasad dotyczących okresu obowiązywania punktów.

Połączenie wyszukiwania dokumentów, generowania odpowiedzi przez LLM i deterministycznych narzędzi ma zwiększyć spójność oraz wiarygodność odpowiedzi. RAG będzie dostarczał kontekst prawny, a narzędzia pomogą przeprowadzać obliczenia i weryfikować określone warunki.
