Oto Twoja kompleksowa notatka podsumowująca wszystkie zagadnienia z notebooka `06_context.ipynb`.

---

## 🧠 Podsumowanie: Filtracja Kontekstowa

### 1. Pojęcia Podstawowe: Kontekst i Konwolucja

* **Filtracja Kontekstowa:** Operacja, w której nowa wartość danego piksela jest obliczana na podstawie jego oryginalnej wartości ORAZ wartości pikseli z jego **sąsiedztwa (kontekstu)**.
* **Maska (Kernel):** Mała matryca (np. 3x3, 5x5) definiująca filtr. Zawiera współczynniki (wagi), które określają, jak bardzo każdy sąsiad wpływa na wynik.
* **Konwolucja (Splot):** Matematyczna operacja "przesuwania" maski po całym obrazie. Dla każdego piksela:
    1.  Maska jest na nim "centrowana".
    2.  Wartość każdego piksela pod maską jest mnożona przez odpowiadającą mu wagę z maski.
    3.  Wszystkie te iloczyny są sumowane.
    4.  Suma ta staje się nową wartością centralnego piksela w obrazie wyjściowym.
    *Jest to operacja **liniowa** (suma ważona).*

---

### 2. 📉 Filtry Liniowe Dolnoprzepustowe (Wygładzanie/Rozmywanie)

Ich celem jest **redukcja szumów** i **wygładzanie** obrazu. Działają poprzez uśrednianie wartości pikseli.

**Dlaczego "Dolnoprzepustowe"?**
* W obrazie **niskie częstotliwości** to gładkie obszary (np. niebo).
* W obrazie **wysokie częstotliwości** to gwałtowne zmiany (krawędzie, detale, szum).
* Filtry te **przepuszczają** niskie częstotliwości (zachowują gładkie tła), a **tłumią** (usuwają) wysokie częstotliwości. Dlatego obraz staje się rozmyty.

#### A. Filtr Uśredniający (Prosty)
* **Maska:** Wszystkie wagi są identyczne (np. dla maski 3x3, każda waga to 1/9).
* **Działanie:** Oblicza prostą **średnią arytmetyczną** wszystkich pikseli w oknie maski.
* **Efekt:** Rozmycie obrazu. Skuteczny dla szumu losowego, ale tworzy "kwadratowe" artefakty i mocno rozmywa krawędzie.
* **Moduł Różnicy (`|Oryginał - Rozmyty|`):** Pokazuje dokładnie to, co filtr usunął – czyli **wysokie częstotliwości** (krawędzie, szum).

#### B. Filtr Gaussa
* **Maska:** Wagi **nie są równe**. Są wyliczane na podstawie funkcji Gaussa (krzywa dzwonowa).
* **Działanie:** Oblicza **średnią ważoną**.
    * Największą wagę ma piksel centralny.
    * Im dalej piksel jest od centrum, tym jego waga (wpływ na wynik) jest mniejsza.
* **Kontrola:** Siłą rozmycia steruje się głównie przez parametr **`sigma (σ)`**. Duża `sigma` = szeroki "dzwon" = mocne rozmycie.
* **Efekt:** Znacznie lepszy od filtra uśredniającego. Daje "naturalne", gładkie rozmycie bez kwadratowych artefaktów. Jest to preferowany filtr dolnoprzepustowy.

---

### 3. 🚦 Filtry Nieliniowe (Specjalistyczne Usuwanie Szumu)

Działają na podstawie operacji statystycznych w oknie, a **nie** na zasadzie konwolucji (sumy ważonej).

#### A. Filtr Medianowy
* **Działanie:**
    1.  Weź wszystkie wartości pikseli z okna maski (np. 9 wartości z 3x3).
    2.  **Posortuj** je rosnąco.
    3.  Wybierz wartość **środkową (medianę)**.
    4.  Ta mediana staje się nową wartością piksela.
* **Zastosowanie:** Absolutnie najlepszy do usuwania szumu impulsowego, tj. **"sól i pieprz"** (losowe białe i czarne kropki).
* **Zaleta:** Doskonale **zachowuje krawędzie** (nie rozmywa ich), ponieważ skrajne wartości szumu są po prostu ignorowane (lądują na początku lub końcu posortowanej listy).
* **Wersja kolorowa:** Najczęściej polega na zastosowaniu filtra medianowego oddzielnie dla każdego z kanałów R, G i B.

---

### 4. 📈 Filtry Liniowe Górnoprzepustowe (Wykrywanie Krawędzi)

Ich celem jest **wykrywanie i podkreślanie** gwałtownych zmian (krawędzi, detali, szumu). Robią odwrotność filtrów dolnoprzepustowych.

**Dlaczego "Górnoprzepustowe"?**
* **Przepuszczają** wysokie częstotliwości (krawędzie), a **tłumią** (usuwają) niskie częstotliwości (gładkie tła).
* **Właściwość maski:** Suma wag w masce wynosi zazwyczaj **0**. Oznacza to, że na gładkim obszarze (gdzie wszystkie piksele są takie same) wynik operacji wyniesie 0 (czerń).

#### A. Laplasjan (Operator Laplace'a)
* **Koncepcja:** Oblicza **drugą pochodną** obrazu.
* **Działanie:** Reaguje bardzo silnie dokładnie w miejscu "przejścia" krawędzi.
* **Maska (np. 4-sąsiedztwo):** `[[0, 1, 0], [1, -4, 1], [0, 1, 0]]` (suma wag = 0)
* **Efekt:** Tworzy obraz z cienkimi, ostrymi liniami na krawędziach. Jest **bardzo wrażliwy na szum**.

#### B. Operatory Gradientowe (Sobel, Prewitt, Roberts)
* **Koncepcja:** Obliczają **pierwszą pochodną** obrazu, czyli **gradient**.
* **Gradient:** Wektor wskazujący kierunek i siłę (magnitudę) najszybszej zmiany jasności. Mówiąc prościej: pokazuje, "jak stroma" jest krawędź.
* **Działanie:** Używają **dwóch masek** do obliczenia dwóch obrazów:
    1.  **Gx:** Reaguje na zmiany w poziomie (wykrywa krawędzie pionowe).
    2.  **Gy:** Reaguje na zmiany w pionie (wykrywa krawędzie poziome).
* **Rodzaje:**
    * **Sobel:** Najpopularniejszy. Maska 3x3 z wagami `[1, 2, 1]`, co czyni go bardziej odpornym na szum.
    * **Prewitt:** Maska 3x3 z wagami `[1, 1, 1]`. Prostszy od Sobela, ale wrażliwszy na szum.
    * **Roberts:** Maska 2x2. Najprostszy, najszybszy, ale też najmniej dokładny i najbardziej wrażliwy na szum.

#### C. Filtr Kombinowany (Obraz Krawędzi)
* **Problem:** Mamy dwa obrazy (`Gx` i `Gy`). Jak stworzyć jeden obraz (mapę krawędzi) pokazujący siłę gradientu niezależnie od kierunku?
* **Rozwiązanie:** Obliczamy **magnitudę (długość) wektora gradientu** dla każdego piksela.
* **Metoda 1 (Dokładna):** Pierwiastek z sumy kwadratów.
    `Magnituda = sqrt( Gx² + Gy² )`
* **Metoda 2 (Przybliżona, szybsza):** Suma modułów (wartości bezwzględnych).
    `Magnituda = |Gx| + |Gy|`
* **Wynik:** Obraz w skali szarości, gdzie jasne piksele oznaczają silne krawędzie, a czarne piksele oznaczają gładkie obszary.