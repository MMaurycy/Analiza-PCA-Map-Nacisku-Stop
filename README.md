# Analiza Głównych Składowych (PCA) dla Zbioru Map Nacisku Stóp

**Autor:** Marcin Przybylski
**Data:** 2 maja 2025

## Opis Projektu

Niniejszy projekt przedstawia analizę zbioru danych map nacisku stóp przy użyciu metody Analizy Głównych Składowych (Principal Component Analysis - PCA). Celem było zrozumienie i praktyczne zastosowanie PCA do analizy rzeczywistego zbioru danych biomedycznych, składającego się z obrazów reprezentujących rozkład nacisku stopy na podłoże, pochodzących z pomiarów dla różnych pozycji ciała (przysiad, skłon, stanie).

Główne cele analizy obejmowały:
* Zrozumienie struktury danych i podstawowych charakterystyk zbioru map nacisku stóp.
* Zastosowanie PCA do redukcji wymiarowości danych, przy zachowaniu maksymalnej ilości informacji (wariancji).
* Wizualizację głównych składowych ("Eigenfeet") w celu zrozumienia głównych kierunków zmienności w danych.
* Ocenę jakości rekonstrukcji danych na podstawie zredukowanej reprezentacji.
* Identyfikację najbardziej charakterystycznych (najbardziej różniących się od siebie) map nacisku stóp w zbiorze za pomocą analizy odległości w przestrzeni PCA.

## 📂 Zasoby Projektu

Prace zostały zrealizowane w środowisku Google Colaboratory.

## 🔬 Podstawy Teoretyczne PCA

Analiza Głównych Składowych (PCA) to technika statystyczna i algorytm uczenia maszynowego bez nadzoru, stosowany głównie do redukcji wymiarowości. Działa poprzez transformację liniową oryginalnych, potencjalnie skorelowanych zmiennych, w nowy zestaw nieskorelowanych zmiennych (głównych składowych), uporządkowanych malejąco według ilości wariancji, którą wyjaśniają.

**Główne kroki algorytmu PCA:**
1.  Standaryzacja danych (opcjonalnie, w tym przypadku dane były już wstępnie przeskalowane).
2.  Obliczenie macierzy kowariancji.
3.  Obliczenie wartości własnych i wektorów własnych macierzy kowariancji. Wektory własne wskazują kierunki maksymalnej wariancji, a wartości własne określają wielkość tej wariancji.
4.  Sortowanie głównych składowych według malejących wartości własnych.
5.  Wybór liczby składowych (k) do zachowania, często na podstawie progu wyjaśnionej wariancji.
6.  Transformacja danych poprzez rzutowanie na nową podprzestrzeń zdefiniowaną przez k wybranych wektorów własnych.

**Zalety PCA:**
* Redukcja wymiarowości.
* Usuwanie korelacji.
* Redukcja szumu.
* Identyfikacja wzorców.

**Wady PCA:**
* Wrażliwość na skalę zmiennych (wymaga standaryzacji, jeśli skale są różne).
* Główne składowe mogą być trudne do bezpośredniej interpretacji.
* Założenie o liniowości.
* Utrata informacji przy redukcji wymiarowości.

## ⚙️ Metodologia Analizy

### 1. Wczytywanie i Przygotowanie Danych

* Dane wejściowe (mapy nacisku stóp) pobrano jako plik `.npy`.
* Oryginalny kształt danych: (18, 34, 6588) (wysokość, szerokość, liczba próbek).
* Zmiana kolejności wymiarów do formatu (liczba\_próbek, wysokość, szerokość): (6588, 18, 34).
* Liczba cech (pikseli) na obraz po spłaszczeniu: $18 \times 34 = 612$.
* Ostateczny kształt danych wejściowych do PCA (po spłaszczeniu obrazów): (6588, 612).

### 2. Implementacja PCA

* Wykorzystano klasę `PCA` z modułu `sklearn.decomposition`.
* **Krok 1:** Utworzenie instancji `PCA(n_components=None)` i dopasowanie do danych w celu analizy wyjaśnionej wariancji przez wszystkie możliwe składowe.
* **Krok 2:** Wyznaczenie liczby składowych potrzebnych do wyjaśnienia określonego progu wariancji (np. 95%).
* **Krok 3:** Utworzenie nowej instancji `PCA` z wybraną liczbą składowych i dopasowanie jej.
* **Krok 4:** Transformacja oryginalnych danych do przestrzeni o zredukowanej wymiarowości (`X_stopy_pca`).
* **Krok 5:** Odwrotna transformacja (rekonstrukcja) danych z przestrzeni zredukowanej do przestrzeni oryginalnej (`X_stopy_reconstructed`).

### 3. Identyfikacja Charakterystycznych Stóp

* Obliczono odległości euklidesowe między wszystkimi parami próbek w przestrzeni o zredukowanej wymiarowości (`X_stopy_pca`) przy użyciu `sklearn.metrics.pairwise.euclidean_distances` oraz `scipy.spatial.distance.pdist` i `scipy.spatial.distance.squareform`.
* Zidentyfikowano parę indeksów próbek o największej odległości.

## 🛠️ Wykorzystane Narzędzia i Technologie

* **Język programowania:** Python
* **Biblioteki:**
    * NumPy
    * Matplotlib
    * Scikit-learn (`sklearn.decomposition.PCA`, `sklearn.metrics.pairwise.euclidean_distances`)
    * SciPy (`scipy.spatial.distance.pdist`, `scipy.spatial.distance.squareform`)
* **Środowisko:** Google Colaboratory

## 📊 Wyniki i Interpretacja

### Analiza Wyjaśnionej Wariancji

* Stwierdzono, że znacząca część wariancji jest skoncentrowana w pierwszych kilkudziesięciu składowych.
* Do wyjaśnienia 95% całkowitej wariancji w zbiorze danych wystarczyło zachować **33 główne składowe**.
* Po transformacji z użyciem 33 składowych, dane miały kształt (6588, 33).
* Dokładny procent zachowanej wariancji wyniósł **95.13%**.

### Wizualizacja Głównych Składowych ("Eigenfeet")

* Główne składowe (wektory własne macierzy kowariancji) zostały zwizualizowane poprzez przekształcenie ich z powrotem do oryginalnych wymiarów obrazu ($18 \times 34$). Nazwano je "Eigenfeet", analogicznie do "Eigenfaces".
* "Eigenfeet" pokazują wzorce nacisku, które najbardziej różnicują poszczególne próbki. Pierwsze składowe kodują globalne cechy, kolejne bardziej subtelne różnice.

### Ocena Jakości Rekonstrukcji

* Porównanie 10 losowo wybranych oryginalnych map nacisku z ich rekonstrukcjami na podstawie 33 głównych składowych wykazało, że rekonstrukcje są bardzo zbliżone do oryginałów.
* Główne cechy rozkładu nacisku zostały dobrze zachowane, potwierdzając, że 33 główne składowe wystarczają do reprezentowania większości istotnych informacji.

### Identyfikacja Najbardziej Charakterystycznych Stóp

* Analiza odległości euklidesowych w przestrzeni PCA (33-wymiarowej) pozwoliła zidentyfikować parę map nacisku stóp, które najbardziej różnią się od siebie.
* Porównanie wyników z funkcji `euclidean_distances` (sklearn) i `pdist` (scipy) wykazało niewielkie różnice (prawdopodobnie wynikające z precyzji obliczeń zmiennoprzecinkowych), które jednak nie wpłynęły na identyfikację pary o maksymalnej odległości.
* Największa znaleziona odległość euklidesowa: **2153.83**.
* Odpowiadająca para stóp o oryginalnych indeksach: **1857 i 4308**. Te mapy reprezentują ekstrema zmienności w zbiorze danych, uchwycone przez 33 najważniejsze główne składowe.

## 🏁 Podsumowanie i Wnioski

### Podsumowanie Analizy

Zastosowanie PCA pozwoliło na:
1.  **Zbadanie struktury wariancji:** Większość zmienności wyjaśniona przez niewielką liczbę składowych (33 składowe dla ~95.1% wariancji).
2.  **Redukcję wymiarowości:** Z 612 do 33 wymiarów, przy zachowaniu większości informacji.
3.  **Wizualizację głównych wzorców ("Eigenfeet"):** Zobrazowanie podstawowych wzorców zmienności nacisku stóp.
4.  **Ocenę rekonstrukcji:** Wysoka jakość rekonstrukcji przy użyciu 33 składowych.
5.  **Identyfikację charakterystycznych stóp:** Znalezienie najbardziej różniących się przypadków (indeksy 1857 i 4308).

### Wnioski i Wpływ Własności PCA

* **Skuteczność redukcji:** PCA okazało się efektywne dla zbioru map nacisku stóp.
* **Interpretacja składowych:** "Eigenfeet" dają wgląd w kierunki zmienności, ale ich bezpośrednia interpretacja fizjologiczna może być trudna.
* **Identyfikacja ekstremów:** Analiza odległości w przestrzeni PCA pozwala na obiektywne znalezienie nietypowych stóp, co jest przydatne do dalszej analizy i unikania "klątwy wymiarowości".
* **Ograniczenia liniowości:** PCA zakłada liniowe zależności. Dla złożonych, nieliniowych struktur, inne metody (np. t-SNE, UMAP) mogłyby dostarczyć dodatkowych informacji.
* **Zastosowanie w klasyfikacji:** Zredukowane dane mogą być efektywnym wejściem do modeli klasyfikacyjnych, potencjalnie przyspieszając trening, zmniejszając ryzyko przeuczenia i poprawiając generalizację.

---
