# Synaptoscope: the rebuild prompt, in sections (written 2026-10-04)

## How to use this file

Everything below the line is a prompt for a new Claude Code session opened in
`C:\Users\Daniel Mercado\synaptoscope`. It is written so you can paste it whole
or in pieces:

- **To remake the whole app:** paste Part A, all of Part B, and Part C.
- **To change one part of the app:** paste Part A (always: it says what the
  app is and how to work), the Part B section(s) for what you want changed,
  and Part C. Delete the sections you don't need. Each Part B section stands on
  its own.
- **Whenever the change touches a screen** (almost always), also paste
  **B0** (the interface: layout, look and navigation). It is the rulebook every
  screen follows.
- **To add your own change to a section:** write it under that section's
  **Your changes** heading, in your own words. Anything there overrides the
  section's text above it.

Each Part B section has the same headings:
- **What it is for:** in plain words.
- **The standard:** the published method and its source.
- **Requirements:** what the app must do.
- **Known problems to fix:** what is wrong now.
- **Done when:** how to check it.
- **Your changes:** space for you.

Sources marked *(verify)* are ones I am confident exist but did not re-read
for this file: the session must read them before citing them in the app.

---

# Part A. What Synaptoscope is (always include)

I'm Daniel. Synaptoscope is my desktop app for analyzing slice
electrophysiology: whole-cell patch clamp (current and voltage clamp) and
extracellular field recordings, from Axon `.abf` files (Clampex) and WinLTP
files. It replaces doing these analyses by hand in Clampfit and Excel. The
repository is `C:\Users\Daniel Mercado\synaptoscope` (branch `v3`); the
current code is a working reference for every section below, so read it
before rewriting it, and keep what already works.

## Read first

1. My Obsidian vault at `C:\Users\Daniel Mercado\Brain`: `VAULT-INDEX.md`, then
   `02 - Synaptoscope/Analysis Decisions.md`, `Health Checks.md`,
   `Measurement Methods.md`, `Active Priorities.md`, and the latest daily
   note. **Recorded decisions are mine: don't reverse one without asking me.**
   Where a published standard conflicts with a recorded decision, show me both
   with their sources and let me decide.
2. `CODE_MAP.md` (where everything is), `README.md` (how each number is
   defined), `docs/development/METHODS_AUDIT.md` (every place a pulse, sweep or
   window stands for a recording, with its source).
3. `docs/development/UI_GUIDE.md` (the interface rules from B0, with
   screenshots), once it exists.

## Who uses it and for what

- Me and the members of my lab, at the rig and at a desk. Most are biologists,
  not programmers or statisticians. **Every screen must say what it is doing
  and why, in plain words, and every number must say how it was measured.**
- My own experiments: CA3 pyramidal cells, sIPSCs under carbachol, DSI
  (depolarization-induced suppression of inhibition) with steps to 0 mV;
  optogenetic and electrical evoked responses (single, paired pulses, trains);
  current steps for intrinsic properties and firing; fEPSPs and LTP.
- The lab's data: tens of files per experiment; gap-free recordings up to
  60 min at 20 kHz (70 million samples); episodic files with hundreds of
  sweeps.

## Principles (apply everywhere)

- **Every analysis follows a published method**, and names its source
  (method papers, or standard tools: Clampfit, IPFX, Easy Electrophysiology,
  Mini Analysis, WinLTP). Where the app differs, it says so and why.
- **Flags are evidence, never an edit.** A questionable value is reported
  with a flag that says what is wrong; it is never blanked or dropped silently.
- **Nothing is chosen silently.** If the app picks a pulse, sweep, window or
  baseline to stand for the recording, the screen says which one and why.
- **Units and definitions travel with the numbers:** every column in Results,
  Excel and CSV exports has a definition (`export/definitions.py`).
- **Plain language** in the interface: no internal names on screen.
- **Clean, minimal and easy to navigate.** Each screen does one job, shows
  what that job needs, and hides the rest until asked for. The user always
  knows where they are, what to do next, and how to get back. Minimal means
  *less on screen at once*, never *less the app can do*: nothing is removed
  to make a screen cleaner, it is moved behind "Advanced" or into a menu.
  The rules are in B0.
- **No new dependencies without asking me.** Current stack: Python 3.12,
  numpy, scipy, pandas, pyabf, pyqtgraph (PyQt5), matplotlib (publication
  figures), openpyxl (Excel). Data downloads go to
  `C:\Users\Daniel Mercado\synaptoscope-data`, never into the repository.

## Architecture (keep these layers separate)

| Layer | Folder | Rule |
|---|---|---|
| Reading files | `io/` | ABF, WinLTP, demo `.npz`; one `Recording` object per file |
| Recording identity | `core/` | animal / slice / cell / condition, typed or parsed from paths |
| Analyses | `analysis/` | no Qt; each analysis registers itself for the recording types it applies to (`addons.register`) and returns a result with `cell_measures()`, `result_rows()`, `sweep_rows()` |
| Exports | `export/` | Excel workbook, tidy CSV with a manifest, publication figures |
| Stimulus files | `protocol/` | the stimulus designer's model and its Clampex output |
| Interface | `app/` | PyQt window, Setup pages, Plots, figure builder, manual. **All colours, fonts, sizes and spacing come from one theme module (`app/theme.py`) and one Qt stylesheet built from it; no widget sets its own colour, font or margin.** |

Analyses must be testable without the window, against simulated recordings
with known answers (`simulate.py`, `tools/audit/known_answers.py`).

---

# Part B. The app, section by section (include the ones you need)

## B0. The interface: layout, look and navigation (include with any section that changes a screen)

**What it is for.** Make the app calm to look at and obvious to move
around in, so a biologist at the rig can open a file, see the result and
export it without a tour, and without hunting through crowded panels.

**The standard.**
- Nielsen's 10 usability heuristics (Nielsen Norman Group): visibility of
  system status, match with the real world (the lab's words, not the code's),
  user control and freedom (undo, back, cancel), consistency, error
  prevention, recognition rather than recall, aesthetic and minimalist design,
  help that is short and in context.
- Shneiderman 1996, "The eyes have it" (IEEE Symposium on Visual Languages):
  *overview first, zoom and filter, then details on demand*. This is the
  shape of every data screen.
- Microsoft's Windows app design guidelines (Fluent): layout grid, spacing,
  typography and the Segoe UI system font, since the app runs on Windows.
- WCAG 2.2: text contrast at least 4.5:1 (3:1 for large text and plot
  lines), never colour alone to carry meaning, every control reachable by
  keyboard.
- Tufte, *The Visual Display of Quantitative Information*: remove ink that
  carries no data (heavy frames, dense gridlines, 3-D, shading).
- Okabe & Ito 2008, "Color Universal Design" *(verify)*: a colour-blind-safe
  palette for data series and groups.

**Requirements.**

*Navigation: one path through the work.*
1. **The top-level structure follows the lab's workflow, left to right**, and
   has no more than six or seven destinations, each named for the task:
   e.g. **Files -> Check -> Analyze -> Results -> Figures -> Export**, with
   Help and Settings apart. Propose the exact set after reading the current
   window; merge or rename existing tabs and Setup pages to fit, and show me
   the old-to-new mapping before moving anything.
2. **You are here.** A breadcrumb at the top of every data screen:
   *Experiment > Animal > Slice > Cell > Recording*. Clicking any level goes
   there. The selected recording is the same on every tab.
3. **A next step on every screen.** Each screen has one primary action,
   styled as the only accent-coloured button (e.g. "Analyze 12 recordings",
   "Open in figure builder"). Secondary actions are plain buttons; rare ones
   go in a "More" menu.
4. **Back, undo and cancel always work.** Alt+Left goes back; Ctrl+Z undoes
   identity edits, accept/reject decisions and figure changes; anything that
   runs longer than about a second shows progress and a Cancel button.
5. **Find anything.** Ctrl+K opens a search box over actions, settings,
   recordings and manual entries ("PPR", "series resistance", "export CSV").
6. **Keyboard for the common moves:** next/previous sweep and recording
   (arrow keys, Page Up/Down), zoom to fit, open file, export. Shortcuts are
   shown in menus and tooltips, and listed in the manual.

*Layout: one job per screen.*
7. **Overview first, details on demand.** A data screen opens on the
   overview (the trace and the headline numbers with their definitions); the
   tables, per-sweep values and settings open beside or below it when asked
   for, never all at once.
8. **Progressive disclosure for settings.** Each analysis shows the few
   settings people change (the defaults from the recorded decisions); the
   rest sit under a collapsed "Advanced" section that says how many settings
   differ from the default ("Advanced (2 changed)"). Changed settings are
   marked and have "Reset to default".
9. **A fixed layout skeleton** used by every screen: navigation on the left,
   the main view in the centre, a details/settings panel on the right that
   can be collapsed, a status bar at the bottom (file, sample rate, sweep,
   cursor time and value, background jobs). Panel sizes and collapsed
   states are remembered between sessions.
10. **Empty states teach.** A screen with nothing to show says what it is
    for and what to do ("No recordings yet. Open a file or a folder, or try
    the demo data"), with the button to do it.

*Look: quiet, consistent, readable.*
11. **One spacing scale** (4, 8, 16, 24, 32 px) and one alignment grid;
    labels left-aligned above or beside their control, the same way on every
    screen.
12. **One typeface** (Segoe UI, the Windows system font; a monospace font
    only for numbers in tables if it aligns them better), **three sizes**
    (body, section heading, screen title) and **two weights**.
13. **Colour has a job.** Neutral greys for the interface; one accent colour
    for the primary action, selection and focus; status colours only for
    status (amber = flagged, red = error, green = passed), always with an icon
    and words, never colour alone. Data series and groups use the
    colour-blind-safe palette and keep the same colour for the same group on
    every plot, table and figure.
14. **Plots without clutter:** axes labelled with units, light or no
    gridlines, no boxes around plots, the scale bar or axes the user expects
    for traces, and the caption required by B11.
15. **Flags look the same everywhere:** one small badge style (icon + short
    word, e.g. "Rs high"), with the full reason and its limit and source in
    the tooltip and the details panel.
16. **Light theme by default, a dark theme for the rig** (a darkened room),
    both passing the contrast rules; plots follow the theme.
17. **Fits real screens:** usable without horizontal scrolling at 1366 x 768
    (rig laptop) and sharp at 4K with Windows scaling (Qt high-DPI on).

*Words.*
18. **Buttons are verbs, headings are nouns, labels are the lab's words.**
    "Analyze recording", not "Run"; "Series resistance", not `rs_mohm`.
    Units in every label and column header.
19. **One help pattern everywhere:** a one-line hint under a control when
    it needs one, a "?" that opens a short explanation with an example and a
    link to the manual entry (B15). The same pattern as the figure builder
    (B12); no long paragraphs on the working screens.
20. **Messages say what happened, why, and what to do**: "Could not read
    18815022.abf: the file is still being written by Clampex. It will be
    read when Clampex closes it."

*How to get there.*
21. **Audit before changing.** Screenshot every current screen, count its
    controls, and list for each: its job, its primary action, what could move
    behind "Advanced", and what breaks the rules above. Put this in
    `docs/development/UI_GUIDE.md` and show me the proposed navigation and
    one redesigned screen (mock-up or working) before converting the rest.
22. **Build the theme first** (`app/theme.py` and the stylesheet, item 9's
    skeleton as a reusable widget), then move screens onto it one at a time,
    each with before/after screenshots. No analysis numbers move
    (prove it with `tools/audit/fingerprint.py`).

**Known problems to fix.** Too many controls are visible at once, and the
figure builder's controls don't say what they are for (my report,
2026-10-04, see B12). The audit in item 21 lists the rest.

**Done when.**
- A lab member who has not used the app does three tasks without help and
  without asking where something is: (a) open lab file 18815022 and read its
  paired-pulse ratio and what it means; (b) find which recordings in DSI_data
  are flagged and why; (c) export the Results of one cell to Excel. Note the
  clicks and any hesitation; fix what slowed them and repeat.
- Every screen has one primary action, a breadcrumb, an empty state and no
  more than about seven visible controls before "Advanced".
- Screenshots of every screen at 1366 x 768 and 1920 x 1080, light and dark,
  are in `docs/development/UI_GUIDE.md`, and a contrast check of the theme's
  colour pairs passes WCAG 2.2 AA.
- A search of `app/` finds no colour, font or margin set outside
  `app/theme.py` and the stylesheet.

**Your changes.**
-

## B1. Opening files and recording identity

**What it is for.** Load recordings, and know which animal, slice, cell and
condition each one belongs to, so recordings can be grouped and compared.

**Requirements.**
- Open files, folders and whole experiments; watch a folder while Clampex
  writes into it (live view).
- Read identity from folder and file names with rules the user can see and
  edit; anything can be typed in Recording identity.
- Put a cell's files on one clock from their start times (a baseline file, a
  step file and the file after it line up).
- Long gap-free files must stay responsive: scroll, zoom and re-analyze
  without rescanning every sample.

**Known problems to fix.** -

**Done when.** DSI_data (`OneDrive - Rutgers University\DSI_data`) and a
60-min lab file open, scroll and analyze without a stall over 0.2 s.

**Your changes.**
-

## B2. Recording type and stimulus detection

**What it is for.** Decide what each recording is (current steps, evoked
response, spontaneous events, field, DSI, voltage steps...) and where the
stimulus is, so the right analyses run.

**Requirements.**
- Detect the stimulus from the recorded trigger or light channel, else the
  command, else typed-in times, and say which.
- Show the detected type and why ("a command step family, 33 step sizes, no
  stimulus"), and let the user override it.
- A stimulus with no response time-locked to it is flagged (sham-trigger
  test, already built).

**Your changes.**
-

## B3. Evoked responses: single pulses, paired pulses and trains (current and voltage clamp)

**What it is for.** Measure the synaptic response to each stimulus: how big,
how fast, how reliable, and how it changes from pulse to pulse.

**What "pulse 1" means (explain this in the app).** A sweep can contain
several stimuli: a pair, or a train of 5-20. "Pulse 1" is the response to the
**first stimulus** of each sweep. Today the app averages all the sweeps
together, then measures the first response on that average, and reports it as
the recording's headline amplitude, latency, rise, decay and area. Averaging
first is the standard (Clampfit: average the traces, then measure); pulse 1 is
used because it is the only response not shaped by the pulses before it.
Pulses 2 onward are measured too, but the Results page shows only pulse 2's
amplitude and the paired-pulse ratio, so a train looks like it was analyzed on
pulse 1 alone.

**The standard.**
- Average sweeps, then measure (Clampfit; methods sections: "an average of
  10-30 consecutive sweeps").
- Paired-pulse ratio as P2 / P1 of the averages, i.e. the ratio of means:
  Kim & Alger 2001, *J Neurosci* 21:9608 (the mean of per-sweep ratios "is
  biased in favor of high values").
- Each later pulse measured after removing the previous response's decaying
  tail (already built: decision D1, 2026-09-30).
- Short-term plasticity as Pn / P1 against pulse number; steady state as the
  mean of the last pulses over P1; cumulative amplitude for the readily
  releasable pool (Schneggenburger et al. 1999, *Neuron* 23:399) *(verify)*.
- Failures and potency, CV and 1/CV² (Faber & Korn 1991, *Biophys J*
  60:1288) *(verify)*.
- Current clamp trains: spike probability and latency jitter per pulse
  (fidelity), when the stimulus drives action potentials.

**Requirements.**
- **Every pulse is shown, not just pulse 1.** Results gets a per-pulse table:
  pulse number, time, amplitude, latency, rise, decay, area or charge, Pn / P1,
  success rate, and (current clamp) spike probability. A plot of amplitude
  against pulse number sits beside it.
- **The headline says what it is**, e.g. "Pulse 1 amplitude (first stimulus
  of each sweep, measured on the average of 30 sweeps)". The user chooses
  which pulse or summary (pulse 1, a chosen pulse, the steady state, the train
  mean) is the recording's headline for the Summary plots and the cell table,
  and that choice is printed in Results and in the export.
- Pulse 1 kinetics come with every pulse's kinetics, not instead of them.
- In current clamp, an EPSP with a spike on it is measured below the spike or
  flagged, never mixed silently with subthreshold EPSPs.
- A tooltip and a manual entry explain "pulse 1", "PPR", "Pn / P1",
  "steady state" and "decay-corrected" in plain words, with a picture.

**Known problems to fix.**
- Results shows pulses 3 onward nowhere; they are only in the Train sheets.
- The Overview's "Summary by recording" defaults to pulse 1 amplitude without
  saying why.

**Done when.** On an optogenetic train in current clamp (lab file
19n02012: 8 pulses at 8 Hz) and on a paired-pulse file (18815022: 2 pulses
50 ms apart), Results shows all pulses, the plotted
profile matches the Train sheet, and someone who has never used the app can
say what "pulse 1" means from the screen alone.

**Your changes.**
-

## B4. Spontaneous and miniature events

**What it is for.** Find spontaneous synaptic events (sIPSCs, mEPSCs...) and
report their frequency, amplitude, kinetics and charge, over the whole
recording and over time.

**The standard.** Template matching (Clements & Bekkers 1997, *Biophys J*
73:220) or deconvolution (Pernia-Andrade et al. 2012, *Biophys J* 103:1429);
one template per experiment from its averaged event, applied identically to
every recording (my decision, 2026-09-29); mean amplitude and frequency per
cell as the headline, median beside it (decision D2); kinetics from the
averaged event (D3). Statistics on cells, not pooled events.

**Requirements.** Keep what is built: detection over the whole file or a
chosen stretch, the experiment template, mismatch warnings, events over time,
event review (accept / reject). Show detection on the trace so the user can
judge it.

**Your changes.**
-

## B5. DSI (evoked and from sIPSCs)

**What it is for.** Measure how a depolarizing step suppresses inhibition,
and its time course.

**The standard.** My windows: 10 s before the step, the first 5 s after the
current settles, events per second as the headline, charge beside it (decision
2026-09-29). Makara et al. 2007, *J Neurosci* 27:10211 (10 s / 5 s); Heinbockel
et al. 2005, *J Neurosci* 25:9449 (1 s bins pooled over trials, normalized
between control and the minimum, onset t_DSI and recovery tau). Evoked DSI:
Pitler & Alger 1992 *(verify)*.

**Requirements.** Keep the built analyses (DSI_Events, DSI_Kinetics,
DSI_Timecourse). Say plainly when the protocol cannot give a measure (e.g. the
onset needs a brief stimulus with recording straight through it).

**Your changes.**
-

## B6. Intrinsic properties and firing (current clamp)

**What it is for.** Resting potential, input resistance, membrane time
constant, sag, rheobase, action-potential shape and firing patterns from
current-step families.

**The standard.** IPFX (Gouwens et al. 2019, *Nat Neurosci* 22:1182), with the
recorded departures (steady-state Rin, 10-95% tau window, median over steps).
Spike detection matches IPFX.

**Your changes.**
-

## B7. Field potentials and LTP

**What it is for.** fEPSP slope and amplitude, fibre volley, paired-pulse
facilitation, input-output, and LTP time courses normalized to baseline.

**The standard.** WinLTP's Maximum Slope (WinLTP 3.01 manual 4.11.4.1);
the 20-80% slope as the alternative; baseline = the 10 min before the
induction (decision D4); one slope method per dataset.

**Your changes.**
-

## B8. Voltage-clamp analyses

**What it is for.** Voltage-gated currents (activation, inactivation,
reversal, Boltzmann fits), tonic current, excitatory and inhibitory
conductances, series-resistance tracking.

**The standard.** Leak subtraction as in the Axon Guide (P/N or a linear leak
from steps where channels are closed) *(verify)*; Boltzmann fits on
conductance; tonic current from the event-free side of the all-points
histogram (Glykys & Mody 2007) *(verify)*.

**Known problems to fix.** A leak slope that cannot be told from zero is now
flagged, not removed (2026-09-30): on rig-subtracted data it puts up to
-175 pA into the large steps. Whether to stop removing it is my call (Active
Priorities).

**Your changes.**
-

## B9. Network activity, DMD / Polygon mapping

**What it is for.** Power spectra, ripples, gamma and phase-amplitude
coupling in field recordings; light-pattern (DMD / Polygon) maps of synaptic
input.

**Your changes.**
-

## B10. Recording quality

**What it is for.** Tell the user whether a recording is good enough to use,
by published limits, without removing anything.

**The standard.** Allen Institute QC (aisynphys and IPFX criteria); series
resistance limits from methods sections (recorded in Analysis Decisions with
sources); my limits: holding current 200 pA app-wide, 500 pA in my DSI preset;
Rs 25 MΩ and a 20% change; Rs/Rm 0.15; noise SD 200 pA; resting potential
-55 mV.

**Requirements.** Quality flags use the one badge style from B0 (item 15);
a recording's quality is readable at a glance in the file list (one badge per
recording: passed, flagged, or not checked) with the details one click away.

**Your changes.**
-

## B11. The Plots tab and heat maps

**What it is for.** A quick look at every recording's results, with the
graphs that suit its type (current clamp, voltage clamp, field).

**Requirements.** Each graph has a one-line caption saying what it shows and
what each point is. Heat maps follow the recorded rules (Analysis Decisions,
"Heat maps: one set of rules for every map"). Every graph opens in the figure
builder. Graphs follow B0's plot style (labelled axes with units, light or no
gridlines, no boxes, the same colour for the same group everywhere), and the
tab opens on a small set of the most useful graphs for the recording type,
with the rest one click away rather than all stacked at once.

**Your changes.**
-

## B12. The figure builder

**What it is for.** Making a publication figure from any table the app
produces, with the right plot for the question, and knowing what it shows.

**The standard (read these, cite them in the app's guide).**
- Weissgerber et al. 2015, "Beyond bar and line graphs", *PLoS Biol*
  13:e1002128: show the individual data points for small samples; bar charts
  hide the distribution.
- Lord et al. 2020, "SuperPlots", *J Cell Biol* 219:e202001064: show every
  measurement and the per-replicate (per-cell, per-animal) means, coloured by
  replicate; statistics on the replicate means.
- Aarts et al. 2014, *Nat Neurosci* 17:491: events, sweeps and cells from one
  animal are not independent; analyze at the right level, or with a
  multilevel model (pseudoreplication).
- Ho et al. 2019, "Moving beyond P values", *Nat Methods* 16:565: estimation
  plots show the effect size and its confidence interval.
- Cumming 2014, "The new statistics", *Psychol Sci* 25:7: SEM, SD and 95% CI
  mean different things, and the figure must say which it shows.
- A research-methods text for neuroscience data, read for each plot type's
  standard use: e.g. Motulsky, *Intuitive Biostatistics* (Oxford University
  Press) for error bars, distributions and paired data *(verify the
  edition)*, and Cohen 2014, *Analyzing Neural Time Series Data* (MIT Press)
  for spectra and time-frequency plots.

**Requirements.**
1. **Start from the question, not the controls.** The first thing the user
   picks is what they want to show, and the builder sets up the standard plot
   for it, with one sentence on why:
   - *compare groups or conditions* (e.g. control vs drug): every cell's
     value as a point, the group mean with the chosen error, cells from the
     same animal marked; a paired design draws lines between a cell's values;
     the estimation plot as an option;
   - *change over time* (LTP, drug wash-in, DSI): binned mean ± SEM across
     cells, normalized to the baseline window, the baseline and the drug
     marked;
   - *pulse-by-pulse profile* (paired pulses, trains): Pn / P1 against pulse
     number, one line per cell, the mean on top;
   - *relationship between two measures* (input-output, I-V, amplitude vs
     rise): a scatter, one point per cell or per event, with a fit if chosen;
   - *distribution* (event amplitudes, intervals): cumulative distributions
     per cell or per condition, with a warning that pooled events are not
     independent (Aarts 2014);
   - *a raw example* (a trace or an averaged response).
2. **Every control explains itself.** One line of plain text under each
   control, and a "?" that opens a short explanation with an example. Rename
   the controls so they read as what they do:
   - "Rows" -> "Keep only rows where..."
   - "Normalize y" -> "Show y relative to a baseline (e.g. % of the first
     10 min)"
   - "Join points of" -> "Connect values from the same cell (repeated
     measures)"
   - "Panels across / down" -> "Split into side-by-side panels by..."
   - "Summary ±" -> "Group centre and spread", with each choice explained:
     mean ± SEM (how precisely the mean is known), mean ± SD (how much cells
     differ), median with IQR (skewed data), 95% CI (the range compatible
     with the data).
   - "Bins" -> "Average into time bins of..."
3. **A live caption: "What this figure shows".** Written by the builder as the
   user changes things, ready to paste into a figure legend: what each point
   is, n cells and n animals per group, what the bars or lines are (mean ±
   SEM...), how the data were normalized and binned.
4. **Warnings when a figure would mislead:** error bars over pooled events or
   sweeps instead of cells; SEM computed with n = events; bar charts with
   fewer than about 10 points per group without the points shown; a group
   with n < 3; a y axis that does not start at zero on a bar chart.
5. **A methods guide in the manual:** for each analysis in Part B, the
   standard figure, why it is used, and the source. The builder links to the
   relevant entry.
6. Keep the three sections (Data, Plot, Axes) as a layout, but put the
   question picker above them and the caption below. The Data section reads
   as steps: which table -> which rows -> which time window -> binning ->
   normalization. Once a question is picked, only the controls that matter
   for it are open; the rest are collapsed (B0, item 8), and the figure
   preview stays the largest thing on the screen.

**Known problems to fix.** The Data, Plot and Axes controls are hard to use and
do not say what they are for (my report, 2026-10-04).

**Done when.** A lab member who has not used the builder can make, from my
DSI_data and one lab experiment and without help: (a) a control-vs-drug
comparison with every cell shown, (b) an LTP time course normalized to
baseline, (c) a paired-pulse profile. They can also explain what the error
bars mean from the caption. Test with screenshots.

**Your changes.**
-

## B13. Stimulus designer and the Clampex protocol files

**What it is for.** Design a stimulus (light pulses, trains, electrical
stimulation, current or voltage steps, triggers), see it, and take it to the
rig ready to run in Clampex.

**What it does now.** It writes one Axon Text File (`.atf`) per channel. In
Clampex an `.atf` is only a waveform: the user must build a protocol by hand
(sweep length, sample rate, number of sweeps, start-to-start interval, which
output, triggers) and point its Waveform tab at the `.atf` as a stimulus file.
That is error-prone, and it is not what the rig needs.

**The standard.** Clampex runs **protocol files (`.pro`)**: the acquisition
mode, sample rate, sweep length and count, the start-to-start interval, the
inputs, and each analog output's epoch table (step, ramp, pulse train;
levels, durations and increments per sweep) with the digital outputs per
epoch (pCLAMP 10/11 user guide, Clampex protocol editor) *(verify)*. A
protocol can also take an analog output's waveform from a stimulus file
(`.atf` or `.abf`), for shapes the epoch table cannot express.

**Requirements.**
1. **Export a `.pro` protocol file that opens and runs in Clampex**, with:
   - acquisition: episodic stimulation, sample rate, sweep length, number of
     sweeps and runs, start-to-start interval;
   - each analog output (light driver, command) as an epoch table where the
     stimulus fits one (steps, pulses, trains, ramps, per-sweep increments);
   - triggers (stimulus isolator, LED TTL) as digital outputs on the epochs;
   - shapes the epoch table cannot hold: a `.pro` whose output takes its
     waveform from an `.atf` stimulus file written beside it, and the
     designer says so.
2. **How to build the `.pro` safely.** The `.pro` format is Axon's binary
   protocol format (believed to share the ABF2 header structure; confirm by
   reading one with pyabf). Ask me for `.pro` files saved from Clampex at my
   rig (one per kind: light train, electrical paired pulse, current steps),
   and use them as templates: change only the fields the design sets, keep
   the rig's hardware settings (channels, gains, telegraphs) as saved. Never
   invent the hardware configuration.
3. Keep the `.atf` export as a second option, labelled for what it is
   ("waveform only, for a protocol you build in Clampex").
4. Before writing, check the design against what Clampex allows (epoch count
   per output, sample rate, output range, digital bits) and say what doesn't
   fit and why.
5. Reading back: open a `.pro` (mine or exported) in the designer to see and
   edit it.

**Known problems to fix.** Only `.atf` stimulus files are exported; I need
Clampex protocol files (my report, 2026-10-04).

**Done when.** Each exported `.pro` reads back with pyabf with the same epochs,
and I open it in Clampex at the rig and it runs the designed stimulus (I check
this; the session can't). Until I have checked it at the rig, the app labels
`.pro` export as "test at the rig before use".

**Your changes.**
-

## B14. Exports (Excel, CSV, figures)

**What it is for.** Getting numbers out: the Excel workbook (master and per
slice), tidy CSV with a manifest of definitions, figures at journal sizes.

**Requirements.** Every column defined; the template, quality flags and
chosen headline pulse travel with the numbers; whole-cell and field kept
separate.

**Your changes.**
-

## B15. Help and the manual

**What it is for.** Teach a new lab member to use the app and understand
what each number means.

**Requirements.** Quick start; one manual entry per analysis (what it
measures, the standard and its source, how to read it, common pitfalls); a
methods guide for figures (B12); a glossary of terms (pulse 1, PPR, sweep,
epoch, DSI, Rs...); a map of the app's screens and keyboard shortcuts (B0);
the screenshots regenerated by the build. Every "?" in the app opens the
matching manual entry.

**Your changes.**
-

## B16. Building and installing

**What it is for.** A Windows program to copy to the rig PC.

**Requirements.** `packaging/build.ps1` builds `dist/Synaptoscope-windows.zip`,
runs the tests and a self-test inside the frozen app.

**Your changes.**
-

---

# Part C. How to work (always include)

- Plan in phases; ask me only what is genuinely my call. Plain language, be
  direct; recommend rather than list options, and argue with me when I'm wrong.
- Log the work in the vault as it happens: a daily note from
  `01 - Daily Notes/Daily Note Template.md` in the month's folder,
  timestamped by the real clock (run `date`); update `Active Priorities.md`,
  `Analysis Decisions.md` (new decisions) and `Measurement Methods.md` (each
  method and its source).
- After each phase, in this order:
  - the full test suite, `C:\Users\Daniel Mercado\miniconda3\envs\synaptoscope\python.exe -m pytest -q`
    with `QT_QPA_PLATFORM=offscreen` (about 9 minutes);
  - `tools/audit/regression.py --compare`;
  - `tools/audit/lab_sweep.py <out.json>` if analysis code changed;
  - `tools/audit/known_answers.py <section>` for the analyses touched;
  - a commit on `v3`;
  - the vault log;
  - the build: `powershell -NoProfile -ExecutionPolicy Bypass -File packaging/build.ps1`,
    run from bash. Don't edit source while it runs.
- A change that moves numbers says which numbers and why, checked against
  known answers or my real files. A change that shouldn't move numbers is
  proven with `tools/audit/fingerprint.py save/compare`, the "before" saved on
  unchanged code.
- Run the app and look (screenshots) before telling me something works. A
  script that drives the window needs an `if __name__ == "__main__":` guard.
- Any change to a screen comes with before/after screenshots at 1366 x 768
  and is checked against B0's rules; record what changed in
  `docs/development/UI_GUIDE.md`. A new control needs a reason to be visible
  by default; otherwise it goes under "Advanced".
- Practical notes: bash heredocs lose backslashes, so write edit scripts to
  the scratchpad; check a large text slice before writing it back.
- Keep a plan file in `docs/development/` as the resume point, current as you
  go.
