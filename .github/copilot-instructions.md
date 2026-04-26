# GhostTrack AI Guidelines

Welcome to **GhostTrack** – a simple Python command‑line tool for OSINT by tracking
IPs, phone numbers and usernames. There is only one Python module in the
repository, so the goal of these instructions is to get you productive quickly
and to flag the small conventions that make the code unique.

---
## 🧱 Big‑picture architecture

* The entire application lives in `GhostTR.py`. There is no package structure or
database, just a CLI menu driven by a list of options.
* Global constants supply ANSI colour codes (see top of `GhostTR.py`). The
`is_option` decorator wraps most of the action functions and prints the banner
before each run.
* Core functionality is implemented as simple functions: `IP_Track`, `phoneGW`,
`TrackLu`, `showIP`. Each one:
  * reads from `input()`
  * hits a remote service (`ipwho.is`, `api.ipify.org`, various social URLs) or
    uses the `phonenumbers` library
  * prints results with colour codes.
* The `options` list at the bottom of the file defines the menu. To add a new
  command simply append a dict with keys `'num'`, `'text'`, and `'func'`.
* The `main()`/`execute_option()`/`call_option()` trio handles validation,
  looping and basic error handling (value errors, KeyboardInterrupt).

There are no tests, no build system, and no external configuration – everything
is procedural and executed when `GhostTR.py` is run as a script.

---
## ⚙️ Developer workflows

1. **Environment setup**
   ```sh
   git clone https://github.com/HunxByts/GhostTrack.git
   cd GhostTrack
   pip3 install -r requirements.txt   # requests, phonenumbers
   ```
   Use any modern Python 3 interpreter; the script uses only the standard
   library plus the two packages above.

2. **Running the tool**
   ```sh
   python GhostTR.py
   ```
   On Windows you may have to use `py GhostTR.py` or `python3` depending on your
   installation.

3. **Adding features**
   * Add a function decorated with `@is_option` (decorator defined near top).
   * Insert a new dict in the `options` list with an unused number.
   * Follow existing print style for colour codes and banners.

4. **Debugging**
   * The script prints detailed ANSI‑coloured output to `stderr` for banners
     and to `stdout` for results; redirecting is not normally needed.
   * External network calls use `requests` without timeouts – add network
timeouts or exception handling when enhancing.

There are no automated tests or make/tasks; manual execution is the only way to
exercise the code.

---
## 📐 Project conventions

* Colour constants (`Bl`, `Re`, `Gr`, etc.) are two‑character strings with
  escape codes. Mixed usage occurs throughout the file; use them for any new
  print statements to stay consistent.
* The `is_option` decorator always calls `run_banner()` before the wrapped
  function. Do not duplicate banner logic inside option functions.
* Silent failures are not handled – causing an uncaught exception will exit the
  program. New code should follow the pattern in `TrackLu` (a broad `try/except`
  that prints the error) if user input or network access might fail.
* Input prompts use the same colour variables; replicate that style for new
  menu items.

---
## 🔗 Integration points & dependencies

* **IP information** – `http://ipwho.is/<ip>` returns JSON; fields are accessed
  directly in `IP_Track()` (no validation). Coordinates are cast to `int` for
  the Google Maps URL.
* **Public IP** – fetched from `https://api.ipify.org/`.
* **Phone numbers** – the `phonenumbers` module drives all logic; look at the
  import block at the top to see which functions are used. Default region is
  hardcoded to `"ID"` (Indonesia).
* **Username tracking** – a fixed list of social‑media URL templates is defined
  in `TrackLu()`. HTTP status 200 implies existence; any other status is
  treated as "not found". The template list can be extended easily.

There are no secrets or API keys in the repo.

---
## 📝 File references

* `GhostTR.py` – entire application
* `requirements.txt` – installable dependencies
* `README.md` – user‑facing instructions; the AI agent only needs to keep it in
  sync if usage changes.

---
## ✅ Tips for Copilot/AI agents

* Suggest only minimal patches: add new menu entries, improve network error
  handling, or refactor colour constants into an enum if complexity grows.
* Avoid generating large new modules; the project's scope is deliberately
  small.
* When editing prints, keep the ANSI codes and the `f"{Wh}...{Gr}"` style.
* If you propose adding tests, mention that none currently exist and manual
  run is the default.
* Do not add asynchronous code or complex frameworks – simplicity is a design
  goal here.

---
Please review these instructions and let me know if any areas are unclear or
if you’d like additional guidance on a specific part of the codebase.