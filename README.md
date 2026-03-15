# Sanoma Arkanoid

Nowoczesna, przeglądarkowa wariacja na temat klasycznego **Arkanoida**. Projekt jest napisany w czystym HTML, CSS i JavaScript, bez bundlera i bez frameworków. Gra działa jako pojedyncza strona, ma responsywny interfejs, system poziomów, bonusy, pauzę oraz tablicę wyników opartą o Google Sheets.

## Co to za projekt

`Sanoma Arkanoid` to lekka gra arcade uruchamiana bezpośrednio w przeglądarce. Gracz steruje paletką, odbija piłkę i czyści planszę z cegieł. Po zniszczeniu całej planszy przechodzi na kolejny poziom, a bazowa prędkość piłki rośnie.

Projekt powstał jako statyczna aplikacja front-endowa:

- `index.html` zawiera strukturę strony, HUD, overlaye i kanwę gry,
- `styles.css` odpowiada za wygląd, responsywność i warstwę wizualną,
- `script.js` zawiera całą logikę gry,
- `google-apps-script/Code.gs` obsługuje backend tablicy wyników.

## Najważniejsze funkcje

- klasyczna rozgrywka typu brick breaker / arkanoid,
- stały układ planszy `5 x 8`,
- przechodzenie między poziomami,
- wzrost bazowej prędkości piłki o 7% na każdy kolejny poziom,
- betonowe i diamentowe cegły wymagające wielu trafień na wyższych poziomach,
- system bonusów pozytywnych i negatywnych,
- syntetyczne efekty dźwiękowe w kosmicznym stylu, w tym osobne SFX dla dobrych i złych bonusów,
- pauza uruchamiana klawiszem `P` lub kliknięciem logo,
- tablica wyników z limitem **top 10**,
- lokalna kopia hi-score w `localStorage`,
- synchronizacja wyników z Google Sheets przez Google Apps Script,
- responsywny interfejs dla desktopu i urządzeń mobilnych,
- wersjonowanie widoczne w prawym dolnym rogu.

## Zasady gry

Cel jest prosty: odbijaj piłkę, niszcz cegły i nie pozwól jej spaść pod paletkę.

- Startujesz z **3 życiami**.
- Po zbiciu wszystkich cegieł przechodzisz na następny poziom.
- Każdy kolejny poziom zwiększa bazową prędkość piłki o 7%.
- **Betonowe cegły** pojawiają się na poziomach `2, 4, 6, 8, 10...`.
- **Diamentowe cegły** pojawiają się na poziomach `3, 6, 9, 12...`.
- Plansza ma też progresję layoutów z **niezniszczalnymi jasnoszarymi ściankami**; level 1 jest pusty, a kolejne poziomy zmieniają układ ścian niżej na siatce.
- Po utracie życia aktywne bonusy znikają.
- Po zakończeniu gry możesz zapisać wynik, ale tylko wtedy, gdy trafia do **pierwszej dziesiątki**.

## Sterowanie

### Klawiatura

- `←` / `A` — ruch paletki w lewo
- `→` / `D` — ruch paletki w prawo
- `Spacja` — start piłki / akcja / strzał
- `P` — pauza

### Mysz i touch

- ruch kursora lub palca steruje pozycją paletki,
- kliknięcie / tap uruchamia akcję,
- kliknięcie logo działa jako pauza,
- na ekranach mobilnych overlay tablicy wyników przewija się dotykiem.

## Bonusy

Standardowe bonusy są ukryte losowo w zwykłych kaflach i wypadają dopiero po ich zniszczeniu. W grze występują zarówno bonusy pozytywne, jak i negatywne. Na każdym poziomie dostępny jest pełny zestaw standardowych bonusów, ale bez **+1 życia** i **super piłki**.

Na wyższych poziomach część zwykłych cegieł zastępowana jest także **betonowymi** i **diamentowymi kaflami**. Betonowe wyglądają jak szare, trójwymiarowe bloki z fakturą betonu i wymagają dwóch trafień, zanim znikną. Diamentowe są bardziej kryształowe, mają trzy poziomy wytrzymałości i pękają stopniowo aż do rozbicia po trzecim uderzeniu. Betonowe kafle niosą negatywne **super-bonusy** (`nagła śmierć`, `reset paletki`), a diamentowe tylko pozytywne (`+1 życie`, `super piłka`). Layouty ścian tworzą teraz 9‑planszowy cykl: `1: brak`, `2-3: ściana 1-klockowa`, `4-5: ściana 2-klockowa`, `6-7: ściana 3-klockowa`, `8-9: ściana 4-klockowa`, a potem cykl się powtarza. Ściany rosną pionowo po obu bokach od pozycji `(8,2)` i `(8,7)`. Aktywna **super piłka / fireball** potrafi zniszczyć również te jasnoszare ściany.

### Pozytywne

- **Wydłużenie paletki** — zwiększa szerokość paletki o jeden krok na 11-stopniowej skali (`100% → 125% → 150% → 175% → 200% → 250%`).
- **Klej** — piłka przykleja się do paletki po kontakcie; efekt trwa 15 sekund, a przyklejona piłka i tak odpali się automatycznie po 3 sekundach.
- **Działo** — paletka może strzelać pociskami i zachowuje tę możliwość także po wejściu na kolejny poziom.
- **+1 życie** — zwiększa liczbę żyć, maksymalnie do 3.
- **Super piłka** — przez 5 sekund piłka przebija cegły na swojej drodze.
- **Spowolnienie piłki** — tymczasowo zmniejsza prędkość aż o połowę i daje mocniejsze smużenie.

### Negatywne

- **Skrócenie paletki** — zmniejsza szerokość paletki o jeden krok na tej samej skali (`100% → 90% → 70% → 50% → 30% → 20%`).
- **Przyspieszenie piłki** — tymczasowo zwiększa prędkość o 25% i nadaje smudze czerwony kolor.
- **Nagła śmierć** — zabiera dokładnie jedno życie, tak jak upuszczenie piłki.
- **Reset paletki** — przywraca bazowy rozmiar paletki i zabiera klej oraz armatę.

## Tablica wyników

Gra korzysta z dwóch warstw przechowywania wyników:

1. **Lokalna kopia** w `localStorage` — szybki odczyt i fallback, gdy sieć jest wolna albo backend chwilowo nie odpowiada.
2. **Google Sheets + Google Apps Script** — źródło online dla wspólnej tablicy wyników.

Najważniejsze zasady:

- wyświetlane jest maksymalnie **10 wyników**,
- zapisać można tylko wynik, który kwalifikuje się do top 10,
- nazwa gracza jest normalizowana: przycinana, zamieniana na wielkie litery i ograniczona do 10 znaków.

## Jak uruchomić projekt lokalnie

To statyczna aplikacja webowa, więc najprostsza opcja to otworzyć `index.html` w przeglądarce.

Możesz też uruchomić prosty serwer HTTP, jeśli wolisz testować projekt w bardziej zbliżonych warunkach:

```bash
python3 -m http.server
```

Następnie otwórz stronę pod lokalnym adresem serwera, np. `http://localhost:8000`.

## Struktura repozytorium

```text
.
├── google-apps-script/
│   └── Code.gs
├── history.md
├── index.html
├── README.md
├── script.js
└── styles.css
```

## Leaderboard online

Backend wyników znajduje się w:

- `google-apps-script/Code.gs`

Frontend komunikuje się z nim przez:

- `LEADERBOARD_API_URL` w `script.js`

Obecna implementacja:

- odczytuje top 10 przez `GET`,
- zapisuje wynik przez `POST`,
- przechowuje dane w arkuszu Google Sheets,
- zwraca wyniki posortowane po punktach, poziomie i nazwie.

Jeśli chcesz przenieść leaderboard na inne konto lub inny arkusz, trzeba zaktualizować:

- identyfikator arkusza w `google-apps-script/Code.gs`,
- URL wdrożonego web appa w `script.js`.

## Rozwój projektu

W repozytorium nie ma bundlera, test runnera ani lintera. To świadomie prosty projekt front-endowy, więc większość zmian najlepiej sprawdzać manualnie w przeglądarce.

Przy zmianach warto przetestować:

- sterowanie klawiaturą,
- sterowanie myszą i dotykiem,
- pause / start / continue overlaye,
- zachowanie bonusów,
- zapis i odczyt hi-score,
- wygląd na desktopie i mobile.

## Historia decyzji

Plik `history.md` zawiera kolejne polecenia i decyzje produktowe rozwijające grę. To główne źródło kontekstu dla dalszych zmian mechaniki, wyglądu i UX.

## Autorstwo

Projekt rozwijany iteracyjnie jako przeglądarkowa gra typu arcade z brandingiem **Sanoma** i naciskiem na prostą, szybką rozgrywkę.
