# Claude Code: skany → DXF → model IFC → wizualizacje (macOS)

Polski · [English](README.en.md)

Plugin do Claude Code dla architektów. Piszesz po polsku, co ma powstać, a Claude przerysowuje skany dokumentacji do DXF, buduje model IFC i robi wizualizacje w Blenderze. Skryptów nie trzeba znać — działają w tle.

Wersja dla macOS (najlepiej Apple Silicon: M1 i nowsze). Wersja dla Windows: [claude-architekt-windows](https://github.com/arrzarr81/claude-architekt-windows).

## Instalacja (raz, ok. 20 minut)

Potrzebne jest konto Claude **Pro** (na próbę) albo **Max** (do codziennej pracy). Hasło administratora nie jest potrzebne: wszystko instaluje się w Twoim katalogu domowym.

**Jak otworzyć Terminal:** naciśnij Cmd + spacja, wpisz `Terminal` i naciśnij Enter. Otworzy się okno, w które wpisuje się polecenia. Każde polecenie zatwierdzasz klawiszem Enter.

1. W Terminalu wpisz:

   ```
   curl -fsSL https://claude.ai/install.sh | bash
   ```

2. Zamknij okno Terminala (Cmd + Q) i otwórz je ponownie (tak jak wyżej).
3. Załóż folder na projekty i przejdź do niego:

   ```
   mkdir -p ~/Projekty
   cd ~/Projekty
   ```

   Folder `Projekty` będzie w Twoim katalogu domowym (w Finderze: Idź → Katalog domowy). Nie zakładaj projektów na Biurku ani w Dokumentach, jeśli synchronizuje je iCloud.
4. Wpisz `claude` i zaloguj się kontem Claude. Potem wpisz w Claude Code:

   ```
   /plugin marketplace add arrzarr81/claude-architekt-mac
   /plugin install architekt@claude-architekt-mac
   ```

5. Napisz: **„Przygotuj komputer do pracy.”** Claude sam zainstaluje Pythona, biblioteki i Blendera i sprawdzi, czy działają. Jeśli macOS zapyta o instalację „narzędzi wiersza poleceń”, kliknij „Zainstaluj”. Zawierają one **Git**, w którym plugin zapisuje historię projektu po każdym etapie, więc zawsze można wrócić do wcześniejszej wersji rysunków.

## Nowy projekt

Każdy budynek to osobny folder, bez spacji i polskich znaków w nazwie. W Terminalu wpisz (`Projekt1` zamień na nazwę projektu):

```
mkdir -p ~/Projekty/Projekt1
cd ~/Projekty/Projekt1
open .
```

Ostatnie polecenie otwiera ten folder w Finderze: przeciągnij do niego skany (PDF, JPG). Potem wróć do okna Terminala, wpisz `claude` i napisz „nowy projekt”.

**Powrót do projektu następnego dnia:** otwórz Terminal i wpisz `cd ~/Projekty/Projekt1`, a potem `claude`. Claude zawsze pracuje w folderze, w którym go uruchomiono, i widzi tylko jego zawartość.

## Praca

Przykłady poleceń:

- „Zrób spis arkuszy ze skanu `teczka.pdf`.”
- „Przerysuj rzut parteru ze strony 66 do DXF i porównaj ze skanem.”
- „Zbuduj model IFC z rzutów i przekroju.”
- „Zrób wizualizację od ulicy o złotej godzinie.”
- „Złóż rzuty i wizualizacje w PDF A3.”

Poprawki podawaj konkretnie: „ściana od ogrodu ma 38 cm, na skanie str. 66 jest 30 cm”. Claude pyta o zgodę przed uruchomieniem poleceń. Na początku zatwierdzaj je pojedynczo.

## Wyniki

- **DXF**: AutoCAD, BricsCAD, LibreCAD albo QCAD (w AutoCAD zapisz jako DWG).
- **IFC**: Autodesk Viewer (viewer.autodesk.com), darmowe konto; wgrany plik jest dostępny 30 dni.
- **Rendery i zestawienia** (PNG, PDF): w folderze projektu.

## Długa praca

Przy zmianie tematu wpisz `/handoff`, a potem `/clear`. Stan prac zapisuje się w pliku `HANDOFF.md`, a nowa rozmowa wczytuje go sama.

## Aktualizacja pluginu

W Claude Code: `/plugin marketplace update claude-architekt-mac`.

## Co jest w środku

| Skill | Kiedy działa |
|---|---|
| `przygotuj-komputer` | instalacja Pythona (uv), bibliotek i Blendera, kontrola GPU (Metal) |
| `nowy-projekt` | foldery, `CLAUDE.md` z zasadami, `HANDOFF.md`, git |
| `spis-arkuszy` | przegląd skanów, tabela arkuszy |
| `skan-do-dxf` | przerysowanie rzutu / przekroju / elewacji do DXF, porównanie ze skanem, DWG |
| `model-ifc` | model IFC4 z rzutów, warianty koncepcji |
| `wizualizacja` | IFC → OBJ → Blender (Cycles, Metal), podgląd i finał |
| `zestawienie-pdf` | PDF A3 z arkuszy i renderów |

Do tego komenda `/handoff` i hook, który po starcie, `/clear` i kompaktowaniu wczytuje `HANDOFF.md`.

Dokładność przerysowania ze skanu to zwykle ±10–20 cm: szkic roboczy i baza koncepcji, nie inwentaryzacja.

Licencja MIT.
