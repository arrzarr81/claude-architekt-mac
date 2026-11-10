---
name: przygotuj-komputer
description: Przygotowuje Maca do pracy z pluginem architekt — sam instaluje (bez hasła administratora) Pythona 3.12 przez uv, biblioteki (ezdxf, ifcopenshell, shapely, matplotlib, opencv, pymupdf) i Blendera, sprawdza GPU (Metal) i zapisuje ścieżki w ~/.architekt/config.json. Używaj, gdy użytkownik mówi „przygotuj komputer”, „zainstaluj”, „nie działa Blender / Python”, albo gdy innemu skillowi brakuje config.json. / EN — Prepares the Mac for the architekt plugin — installs (no admin password) Python 3.12 via uv, the libraries (ezdxf, ifcopenshell, shapely, matplotlib, opencv, pymupdf) and Blender, checks the GPU (Metal) and saves paths to ~/.architekt/config.json. Use when the user says “set up the computer”, “install”, “Blender / Python does not work”, or when another skill is missing config.json.
---

# Przygotowanie komputera (macOS)

Użytkownik nie jest programistą: instalujesz wszystko sam, poleceniami w terminalu. Nie każ mu pobierać plików ze stron. Wszystko instaluje się w katalogu domowym, bez hasła administratora.

## 0. Czy już jest gotowe?

Jeśli istnieją `~/.architekt/config.json` i `~/.architekt/venv/bin/python`: uruchom krok 5. Wynik „WSZYSTKO DZIAŁA” → powiedz to użytkownikowi i zakończ.

## 1. Git (narzędzia wiersza poleceń Apple)

`git --version`. Jeśli macOS pokaże okno „Zainstalować narzędzia wiersza poleceń?”, poproś użytkownika, żeby kliknął „Zainstaluj” i dał znać po zakończeniu (kilka minut). Możesz w tym czasie robić kolejne kroki — git jest potrzebny dopiero do commitów w `/handoff`.

## 2. uv (menedżer Pythona)

```
command -v uv || ls ~/.local/bin/uv || curl -LsSf https://astral.sh/uv/install.sh | sh
```

Instaluje się do `~/.local/bin/uv`. W bieżącej sesji używaj pełnej ścieżki `~/.local/bin/uv`.

Jeśli instalator przerwie się błędem (np. `mktemp: ... Operation not permitted` przy włączonym sandboksie Claude Code), pobierz sam program:

```
A=$([ "$(uname -m)" = arm64 ] && echo aarch64 || echo x86_64)
mkdir -p ~/.local/bin && curl -fsSL "https://github.com/astral-sh/uv/releases/latest/download/uv-$A-apple-darwin.tar.gz" | tar -xz -C ~/.local/bin --strip-components 1
```

Test karty graficznej w kroku 5 też może się nie udać przy włączonym sandboksie (Blender nie widzi wtedy GPU). To nie jest błąd instalacji. Render w Blenderze uruchamiaj wtedy poza sandboksem.

## 3. Python 3.12, środowisko i biblioteki

```
~/.local/bin/uv venv --python 3.12 ~/.architekt/venv
~/.local/bin/uv pip install --python ~/.architekt/venv/bin/python ezdxf shapely matplotlib opencv-python-headless ifcopenshell pymupdf
```

uv sam pobiera Pythona 3.12, jeśli go nie ma. Jedno środowisko dla wszystkich projektów — nie twórz `.venv` w folderach projektów.

## 4. Blender

- Najpierw krok 5 — `sprawdz.py` sam szuka `/Applications/Blender.app` i `~/Applications/Blender.app`. Jest → pomiń instalację.
- Jest Homebrew (`command -v brew`): `brew install --cask blender`.
- Bez Homebrew — oficjalny obraz DMG do `~/Applications`:

  ```
  V=$(curl -s https://download.blender.org/release/ | grep -oE 'Blender[0-9]+\.[0-9]+/' | sort -uV | tail -1)
  A=$([ "$(uname -m)" = arm64 ] && echo arm64 || echo x64)
  F=$(curl -s "https://download.blender.org/release/$V" | grep -oE "blender-[0-9.]+-macos-$A\.dmg" | sort -uV | tail -1)
  curl -fL -o "$TMPDIR/blender.dmg" "https://download.blender.org/release/$V$F"
  M=$(hdiutil attach -nobrowse -readonly "$TMPDIR/blender.dmg" | grep -oE '/Volumes/.*' | head -1)
  mkdir -p ~/Applications && cp -R "$M/Blender.app" ~/Applications/ && hdiutil detach "$M" -quiet
  ```

  Ok. 400 MB — powiedz użytkownikowi, że to potrwa kilka minut. Sprawdź, czy wybrana wersja to wydanie stabilne (bez `alpha`/`beta` w nazwie).
- Render na Apple Silicon idzie na GPU (Metal) — `blender_render.py` wybiera go sam.

## 5. Kontrola

```
~/.architekt/venv/bin/python "<folder tego skilla>/scripts/sprawdz.py"
```

(opcjonalnie `--blender "<ścieżka>"`). Skrypt zapisuje `~/.architekt/config.json` i kończy się „WSZYSTKO DZIAŁA” albo listą braków — braki uzupełnij i uruchom ponownie.

## Raport dla użytkownika

Krótko, bez kodu: „Gotowe: Python 3.12, biblioteki, Blender 5.x. Render na GPU (Metal) albo na procesorze. Możesz zacząć: załóż folder projektu ze skanami i napisz »nowy projekt«.”
