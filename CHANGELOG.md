# NAHL Scoreboard Changelog

Versioning: MAJOR.MINOR.PATCH
- PATCH (x.x.1): small fixes and tweaks
- MINOR (x.1.x): larger updates and new features
- MAJOR (1.x.x): major changes or redesigns

## v2.0.0 (2026-09-28)

### Added
- **Live scoreboard.** While your team is playing, the first page becomes a live scoreboard: both teams' logos (away on the left, home on the right), the score, shots on goal for each team, the period and the game clock. The clock counts down on screen every second between updates. During intermissions it shows "1st INT" or "2nd INT" instead of the clock, and "End 3rd", "End OT" or "Shootout" when those apply. The line at the bottom names the last goal scorer, and a red LIVE badge sits at the top left.
- The live data comes from GameSheet's live scoring, the same source gamesheetstats.com uses, and updates every 15 seconds. If live scoring can't be reached, the scoreboard falls back to the NAHL stats feed every 30 seconds. That feed has the score and shots, but no game clock.
- **Screen stays on the game.** While your team's game is on, including intermissions, the pages no longer rotate. You can still tap to look at other pages. The screen comes back to the scoreboard 30 seconds after your last touch.
- When the game ends, the final score ("FINAL", "FINAL/OT" or "FINAL/SO") stays on screen for 5 minutes. Then the pages rotate again, and Latest Result, Recent Results and Standings update to include the game.
- Serial command `live`: shows which game is being watched, its score, shots and clock, and when the next check happens.

### Changed
- **Tapping:** tap the left half of the screen to go back one page, or the right half to go forward one page. Holding 5 seconds on the Settings page still opens setup, and holding 2 seconds on any other page still plays a test goal alert. A short tap on the Settings page's buttons still works as before.
- **Fewer downloads when no game is on.** The schedule and results (every 2 minutes before), the standings (every 30 minutes before) and the news now refresh once every 24 hours. Around game time the scoreboard gets busier by itself:
  - 3 hours before puck drop, it reads the schedule again, in case the start time changed.
  - Starting 30 minutes before the scheduled start, it checks every minute whether the game has started.
  - After the scheduled start, it checks every 30 seconds for the first 30 minutes, then every 2 minutes, for up to 4 hours in case the game is delayed.
  - Between games it makes no requests besides the daily refresh.
- A failed download is retried after 45 seconds, then at longer and longer gaps up to 30 minutes. It no longer waits a full day.
- **Recent Results:** only the result letter is colored now, W in green and L in red. Everything else on the page is white. Overtime and shootout losses count as losses, so they show a red L, with a small white "OT" or "SO" next to the letter. The record line (for example 5-2-1-0) is still there, now in white.
- The goal alert (LED flash and GOAL! banner) now comes from the live data, so it appears within about 15 seconds of the goal being entered. The scorer's name shows when the NAHL feed has it.

### Removed
- The old live mode, which read the schedule every 25 seconds during games. The live scoreboard replaces it.

## v1.3.6 (2026-09-28)

### Fixed
- A download that got cut off partway showed a cryptic "JSON: IncompleteInput" error and wasn't retried until the next round, which is 45 seconds later for scores and standings, or 30 minutes for the update check. Now the scoreboard retries a cut-off or failed download right away, up to two more times (after 2 and 4 seconds). A single dropped connection no longer shows an error.

### Changed
- Error messages now name the feed that failed and say what happened, for example "Schedule: cut off 31/64 KB (closed)", "Standings: HTTP 503", "Update check: read Timeout", or "News: no headlines". They show on the page placeholder, in the footer ("Stale - Schedule: ..."), and on the Settings page for the update check.
- The serial log records, for each download, the bytes received against the expected size, the free memory, the largest free memory block, and the Wi-Fi signal. This helps diagnose failures.

## v1.3.5 (2026-09-28)

### Added
- A Brightness slider for the scoreboard's screen on the phone setup page, in the "4 · Display" section. It goes from 10% to 100% in 5% steps. To open the setup page on a board that is already set up, hold a finger on the Settings page for 5 seconds.
  - The scoreboard's screen follows the slider while you move it. Tap Save to keep the setting.
  - It can't go below 10%, so the screen never goes dark.
  - Boards that have never saved a brightness stay at 100%, the same as before.
  - Like the "Invert colors" option in the same section, the setting is kept after a factory reset.
- The confirmation page shown on the phone after saving ("All set") includes the saved brightness.

## v1.3.4 (2026-09-28)

### Changed
- The "Invert colors" option now lives only on the phone setup page, in the "4 · Display" section. The colors change when you tap Save. To reach the setup page on a board that is already set up, hold a finger on the Settings page for 5 seconds.
- The Invert button on the Settings page is gone, and the page looks as it did in v1.3.2.
- Tapping the setup screen no longer flips the colors, and its "Colors look wrong? Tap the screen." line is gone.
- The setting is still saved on the board and a factory reset still keeps it.

## v1.3.3 (2026-09-28)

### Added
- "Invert colors" option for boards whose screen looks like a photo negative (common on some CYD boards, often the version with two USB ports). The colors flip right away and the choice is saved. There are four ways to change it:
  - Tap the new **Invert** button on the Settings page, on the right under the gear.
  - Tap the screen while the setup screen is showing. The setup screen says "Colors look wrong? Tap the screen."
  - Tick **Invert colors** on the phone setup page, in the new "4 · Display" section.
  - Send `invert` on the serial port.

### Changed
- A factory reset keeps the "Invert colors" choice. It belongs to the screen, not to the user, and keeping it means the reset and setup screens still show the right colors.

## v1.3.2 (2026-09-28)

### Fixed
- The first over-the-air update could fall back to the old version by itself. A new version only counted as working after its whole first round of downloads (season, schedule, standings, and news) had finished. Any restart before then made the scoreboard go back to the previous version, for example a crash, a power dip, a replug, the reset button, or opening the Arduino Serial Monitor. A new version now counts as working after its first successful download, or after it has been connected to Wi-Fi and running for 1 minute, whichever comes first.
- A new version is no longer sent back after 10 minutes just because the data sources or the internet were down. It only goes back to the previous version if it crashes or restarts before it has shown that it works, or if it can't connect to Wi-Fi and the setup hotspot goes unused for 10 minutes.
- Opening setup (hold on the Settings page) or a factory reset right after an update no longer rolls the update back.
- Tapping "Tap to install" again while an update was starting could queue it twice. The progress screen now appears right away and further taps are ignored.
- Long downloads could starve a system task on the network core and trigger a watchdog restart. The scoreboard now gives it time during downloads.
- The Wi-Fi driver no longer writes its own copy of the Wi-Fi settings to flash. They are already saved with the scoreboard settings.

### Added
- Update diagnostics. If an update falls back to the previous version, the Settings page shows "Update to vX failed, rolled back" on the Updates line. It alternates with the reason, for example "Reason: crash after 23 s (news fetch)". The message stays until the next successful update.
- Every start logs the reset reason, and what the previous start was doing when it ended, on the serial port. The `ota` command prints it again.

## v1.3.1 (2026-09-28)

### Changed
- The Settings page footer shows "OTA test OK" in green. This small change confirms that over-the-air updates work.

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
