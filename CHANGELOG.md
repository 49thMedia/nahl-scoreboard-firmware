# NAHL Scoreboard Changelog

Versioning: MAJOR.MINOR.PATCH
- PATCH (x.x.1): small fixes and tweaks
- MINOR (x.1.x): larger updates and new features
- MAJOR (1.x.x): major changes or redesigns

## v1.3.0 (2026-09-28)

### Added
- Over-the-air updates. The scoreboard checks for new firmware shortly after it starts, once a day, and when you tap through to the Settings page.
- When an update is available, the Settings page shows the new version and its notes, plus a "Tap to install" button. Tap it twice to confirm. The Settings dot at the bottom of the screen turns orange.
- A progress screen appears while the update installs. The download is checked for the right size and checksum (SHA-256) before it is installed. If anything is wrong, the update is cancelled and the scoreboard keeps its current version.
- Automatic rollback: if a new version crashes or restarts before it has connected and loaded data, or hasn't managed that within 10 minutes, the scoreboard goes back to the previous version by itself.
- "Install updates automatically" option on the setup page (off by default). When it is on, updates install between 2 and 4 AM, and never during a game.
- If an update needs a newer starting version than the board has, the Settings page shows "Update requires USB" instead of installing.
- Serial commands: `ota`, `ota check`, and `ota install`.

### Changed
- The Settings page shows the firmware version, the update status, and when updates were last checked. The device ID moved to the bottom right of the page.

## v1.2.0 (2026-09-28)

### Added
- Time zone picker on the phone setup page, with US and Canada zones: Hawaii, Alaska, Pacific, Mountain, Arizona (no daylight saving time), Central, Eastern, Atlantic, and Newfoundland.
- The time zone starts on the chosen team's home zone. The page updates it when the team changes, unless the zone was changed by hand.
- The Settings page shows the time zone and its current abbreviation (for example AKDT). The serial `info` command prints it too.

### Changed
- The clock, game times, news dates, and "Updated" times use the chosen time zone instead of the built-in Alaska time. The zone abbreviation next to game times comes from the time zone itself.
- Boards set up before v1.2.0 have no saved time zone, so they use the team's home zone until setup is run again.
- The Settings page hint now reads "Hold here 5 s to change settings".

### Fixed
- A format warning in the schedule log message on ESP32 Arduino core 3.x.

## v1.1.0 (2026-09-28)
First release of the NAHL Scoreboard for the ESP32-2432S028R ("Cheap Yellow Display").

### Added
- First-time setup: the board opens its own Wi-Fi hotspot with a QR code, and a phone setup page picks the home Wi-Fi network, the password, and a favorite NAHL team. The settings are saved in the board's memory.
- Support for every NAHL team, with built-in logos for all 37 teams. Scores, schedule, standings, news, and goal alerts follow the chosen team.
- Live scoreboard on page 1 with the team and opponent logos, the score, the period, shots on goal, and the last goal scorer.
- Goal alerts: the rear LED flashes red and a "GOAL!" banner appears when the chosen team scores.
- Pages for the next game with a countdown, recent results, division standings, team news, and settings.
- Settings page: hold for 5 seconds to change the team or Wi-Fi. Hold the screen for 3 seconds at power-on to do a factory reset.
- Serial commands: `g` and `o` for test goals, plus `setup`, `reset`, and `info`.

### Changed
- News refreshes once a day instead of every 30 minutes.

### Fixed
- Builds on ESP32 Arduino core 3.x as well as 2.0.17. The web server headers are now included before TFT_eSPI.
