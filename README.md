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
- wzrost bazowej prędkości piłki o 10% na każdy kolejny poziom,
- betonowe cegły wymagające 2 trafień na wyższych poziomach,
- system bonusów pozytywnych i negatywnych,
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
- Każdy kolejny poziom zwiększa bazową prędkość piłki.
- Od poziomu 2 pojawiają się **betonowe cegły**. Każdy kolejny poziom dodaje jedną taką cegłę więcej.
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

Bonusy są ukryte w wybranych cegłach i wypadają dopiero po ich zniszczeniu. W grze występują zarówno bonusy pozytywne, jak i negatywne.

Na wyższych poziomach część zwykłych cegieł zastępowana jest także **betonowymi kaflami**. Wyglądają jak szare, trójwymiarowe bloki z fakturą betonu i wymagają dwóch trafień, zanim znikną.

### Pozytywne

- **Wydłużenie paletki** — zwiększa szerokość paletki skokowo.
- **Klej** — piłka przykleja się do paletki po kontakcie; efekt trwa 15 sekund, a przyklejona piłka i tak odpali się automatycznie po 3 sekundach.
- **Działo** — paletka może strzelać pociskami; efekt trwa 15 sekund.
- **+1 życie** — zwiększa liczbę żyć, maksymalnie do 3.
- **Super piłka** — przez 5 sekund piłka przebija cegły na swojej drodze.
- **Spowolnienie piłki** — tymczasowo zmniejsza prędkość.

### Negatywne

- **Skrócenie paletki** — zmniejsza szerokość paletki.
- **Przyspieszenie piłki** — tymczasowo zwiększa prędkość.

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
