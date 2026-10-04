# Clampwise: the rebuild prompt, in sections (written 2026-10-04)

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
- **Whenever the change touches how data are stored, loaded, cached or
  batched, or adds an analysis**, also paste **B18** (foundations for scale)
  and **B21** (shared fitting and signal tools).
- **B17** (the rename) is a one-time job: drop it once it is done.
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

# Part A. What Clampwise is (always include)

I'm Daniel. **Clampwise** (called Synaptoscope until October 2026) is my
desktop app for analyzing slice electrophysiology: whole-cell patch clamp
(current and voltage clamp) and extracellular field recordings, from Axon
`.abf` files (Clampex) and WinLTP files. It replaces doing these analyses by
hand in Clampfit and Excel, and helps at the rig while I patch. The
repository is `C:\Users\Daniel Mercado\synaptoscope` (branch `v3`); the
current code is a working reference for every section below, so read it
before rewriting it, and keep what already works. The repository folder, the
conda environment and the vault folder keep the old name until I move them
(B17); the paths in this prompt are the real ones.

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
4. The reference library below: the chapters listed for the section you are
   working on.

## Reference library (the books and papers the app is built on)

The app's methods come from these. I confirmed that each one exists and its
publication details; the contents listed are what each is known for, not a
re-read, so **read the chapter before building from it or citing it in the
app**, and cite chapter or page in the manual. Don't copy text from the books
into the app; explain in our own words and cite. If a book is not available
to you, say so and work from the method papers it cites. Where a book and a
recorded decision disagree, show me both.

*Patch-clamp practice*

| Source | What the app takes from it | Sections |
|---|---|---|
| Sakmann & Neher (eds) 1995, *Single-Channel Recording*, 2nd ed (Plenum; Springer reprint) | Marty & Neher on whole-cell recording; Sigworth on the amplifier and series-resistance compensation; Gillis on capacitance measurement; Colquhoun & Sigworth on fitting and the statistics of records | B19, B20, B21 |
| Walz (ed) 2007, *Patch-Clamp Analysis: Advanced Techniques*, 2nd ed, Neuromethods 38 (Humana) | whole-cell and perforated-patch recording, fast drug application, the analysis of each | B19, B20 |
| Molleman 2003, *Patch Clamping: An Introductory Guide to Patch Clamp Electrophysiology* (Wiley) *(verify)* | plain explanations of the seal, membrane test and recording modes, for the manual | B15, B19 |
| *The Axon Guide* (Molecular Devices) *(verify the edition)* | membrane test, Rs compensation and its errors, leak subtraction, filtering and sampling, liquid junction potential | B8, B19, B20, B21 |

*Cellular biophysics and neural computation*

| Source | What the app takes from it | Sections |
|---|---|---|
| Johnston & Wu 1995, *Foundations of Cellular Neurophysiology* (MIT Press) | passive membrane and cable properties, space clamp, quantal analysis of transmission | B6, B20, B22 |
| Hille 2001, *Ion Channels of Excitable Membranes*, 3rd ed (Sinauer) | conductance, reversal potential, Boltzmann activation, GHK | B8 |
| Koch 1999, *Biophysics of Computation* (Oxford) | dendritic filtering of synaptic inputs, limits of the space clamp, synaptic integration | B20 |
| Dayan & Abbott 2001, *Theoretical Neuroscience* (MIT Press) | firing rate, interspike-interval statistics, CV, f-I curves | B6, B4 |
| Gerstner, Kistler, Naud & Paninski 2014, *Neuronal Dynamics* (Cambridge; free online) | adaptation, firing patterns, fitting simple neuron models to recordings | B6 |
| Izhikevich 2007, *Dynamical Systems in Neuroscience* (MIT Press) | firing-pattern classes (tonic, adapting, bursting, delayed), excitability types and rheobase | B6 |

*Data analysis*

| Source | What the app takes from it | Sections |
|---|---|---|
| Kass, Eden & Brown 2014, *Analysis of Neural Data* (Springer) | estimation with uncertainty, the bootstrap, regression, point processes for spike and event trains | B4, B23 |
| Nylen & Wallisch 2017, *Neural Data Science: A Primer with MATLAB and Python* (Academic Press) | the analysis cascade (import, preprocess, analyze, visualize) as a pipeline; Python practice | B18 |
| Cohen 2014, *Analyzing Neural Time Series Data* (MIT Press) | filtering, spectra, time-frequency analysis | B9, B21 |
| Mitra & Bokil 2008, *Observed Brain Dynamics* (Oxford) | multitaper spectra with confidence intervals | B9 |
| Motulsky & Christopoulos 2004, *Fitting Models to Biological Data Using Linear and Nonlinear Regression* (Oxford) | fitting, weighting, comparing models, confidence intervals of fitted parameters | B21 |
| Motulsky, *Intuitive Biostatistics* (Oxford) | error bars, paired data, which test (already in B12) | B12, B23 |

*Method papers used by the new sections*

- Neher 1992, *Methods Enzymol* 207:123; Barry 1994, *J Neurosci Methods*
  51:107 (JPCalc): liquid junction potential (B20).
- Williams & Mitchell 2008, *Nat Neurosci* 11:790: voltage-clamp errors in
  central neurons (B20).
- Golowasch et al. 2009, *J Neurophysiol* 102:2161: capacitance depends on
  how it is measured in non-isopotential neurons (B20).
- Traynelis 1998, *J Neurosci Methods* 86:25: offline series-resistance
  correction *(verify)* (B20).
- Silver 2003, *J Neurosci Methods* 130:127 (multiple-probability fluctuation
  analysis); Clements 2003, *J Neurosci Methods* 130:115 (variance-mean
  analysis) *(verify)*; Saviane & Silver 2006, *J Neurosci Methods* 153:250
  (errors in the variance) (B22).
- Sigworth 1980, *J Physiol* 307:97 and Traynelis et al. 1993, *Neuron*
  11:279: non-stationary and peak-scaled fluctuation analysis *(verify)* (B22).
- Saravanan, Berman & Sober 2020, *Neurons, Behavior, Data analysis, and
  Theory* 3(5): the hierarchical bootstrap for nested data (B23).

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
- **Clampex and WinLTP only.** The app reads Axon `.abf` files from Clampex
  and WinLTP files, writes Clampex stimulus (`.atf`) and protocol (`.pro`)
  files, and exports Excel and CSV. Don't add other recording formats (HEKA,
  Igor, NWB and the like) or build for them; depth on these two comes first.
- **No new dependencies without asking me.** Current stack: Python 3.12,
  numpy, scipy, pandas, pyabf, pyqtgraph (PyQt5), matplotlib (publication
  figures), openpyxl (Excel). Data downloads go to
  `C:\Users\Daniel Mercado\synaptoscope-data`, never into the repository.

## Architecture (keep these layers separate)

| Layer | Folder | Rule |
|---|---|---|
| Reading files | `io/` | ABF, WinLTP, demo `.npz`; one `Recording` object per file |
| Recording identity | `core/` | animal / slice / cell / condition, typed or parsed from paths |
| Project store | `project/` | one experiment's index, settings, review decisions, result cache and provenance (B18) |
| Shared tools | `analysis/tools/` | filters, baselines, fits, kinetics definitions, used by every analysis (B21) |
| Analyses | `analysis/` | no Qt; each analysis registers itself for the recording types it applies to (`addons.register`) and returns a result with `cell_measures()`, `result_rows()`, `sweep_rows()` |
| Exports | `export/` | Excel workbook, tidy CSV with a manifest, publication figures |
| Stimulus files | `protocol/` | the stimulus designer's model and its Clampex output |
| Interface | `app/` | PyQt window, Setup pages, Plots, figure builder, manual. **All colours, fonts, sizes and spacing come from one theme module (`app/theme.py`) and one Qt stylesheet built from it; no widget sets its own colour, font or margin.** |

Analyses must be testable without the window, against simulated recordings
with known answers (`simulate.py`, `tools/audit/known_answers.py`), and
runnable from the command line with the same numbers as the window (B18).

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

*The model: Claude's interface.* I want the app to feel like Claude's app
(claude.ai): calm, uncluttered, trustworthy, with the content in front and
the controls out of the way. Borrow the *style*, not the brand: no Anthropic
or Claude names, logos or licensed fonts. What that means here:
- a warm off-white background (and a warm dark grey in the dark theme), not
  pure white or grey-blue; panels separated by space and hairline borders,
  not boxes and bevels;
- one warm accent colour (a muted terracotta or clay) used sparingly, for the
  primary action, selection and focus only;
- generous whitespace, content in a comfortable reading width, soft rounded
  corners (about 6-8 px) on panels, buttons and inputs, no gradients;
- a slim, collapsible left sidebar for navigation and recent projects, like
  Claude's conversation list; the main area holds one thing at a time;
- clear type: a clean sans-serif for the interface, readable sizes, numbers
  aligned in tables;
- quiet motion only where it helps (a panel sliding open), never animation
  that delays work;
- plots styled to match: the same background, thin axis lines, the accent and
  the colour-blind-safe palette for data, labels in the interface font, so a
  graph looks like part of the page rather than a pasted-in widget.
Reliability comes before looks: rendering must never slow analysis or
scrolling (B1's 0.2 s limit), and every visual choice lives in
`app/theme.py`. PyQt5 can do all of this with a stylesheet, a custom
sidebar widget and pyqtgraph theming; say where Qt can't match it (e.g.
soft shadows are costly) and choose the simpler option.

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
judge it. Report the timing of events as well as their size: the
inter-event-interval distribution, its CV, and whether the events look like
a steady random (Poisson) process or come in bursts (Kass, Eden & Brown;
Dayan & Abbott), so a frequency change can be told from a change in
burstiness. Accepted events feed fluctuation analysis (B22).

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
Spike detection matches IPFX. Firing patterns from Izhikevich 2007 and
Gerstner et al. 2014; interspike-interval statistics and f-I curves from
Dayan & Abbott 2001; passive properties from Johnston & Wu 1995.

**Requirements.**
- Keep what IPFX-style analysis already gives, and add the firing
  description the books use: f-I curve with its slope (gain) and rheobase;
  first-spike latency; adaptation index and ISI ratio (last / first); ISI CV;
  and a firing-pattern label (tonic, adapting, bursting, delayed, stuttering)
  with the rule that set it printed beside it, never a label alone.
- Membrane tau and Rin come with the fit shown on the trace and the method
  named (B21); sag ratio says which step and window it used.
- Spike shape (threshold, peak, half-width, AHP, max rise and fall rates) per
  spike and as the first-spike-at-rheobase headline, saying which spike.

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

**The standard.** Multitaper spectra with confidence intervals (Mitra &
Bokil 2008); filtering, time-frequency and phase-amplitude coupling as in
Cohen 2014. Filters and their settings come from the shared tools (B21) and
are listed with the result.

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
matching manual entry. A "Further reading" page lists the reference library
(Part A) by topic, with what each book is good for, so a new lab member knows
where to learn the method behind a number. A short "Patching with Clampwise"
guide walks through a rig session (B19).

**Your changes.**
-

## B16. Building and installing

**What it is for.** A Windows program to copy to the rig PC.

**Requirements.** `packaging/build.ps1` builds `dist/Clampwise-windows.zip`,
runs the tests and a self-test inside the frozen app.

**Your changes.**
-

## B17. Renaming the app to Clampwise (one-time)

**What it is for.** The app is now called **Clampwise**. Everything a user
sees, and everything the app writes, should say so, without breaking
anything made under the old name.

**Requirements.**
1. **On screen and in the build:** window title, About box, splash screen,
   manual, README and docs, the executable and `dist/Clampwise-windows.zip`.
2. **In what the app writes:** the "made with" field of the export manifest,
   Excel workbook properties and figure metadata say "Clampwise <version>".
3. **The Python package** is renamed `clampwise`; a small `synaptoscope`
   package stays behind that imports from `clampwise` and warns once, so my
   old scripts and notebooks keep running.
4. **Nothing made before is lost:** saved settings are copied from the old
   Qt settings name on first start; projects, presets, event templates and
   session files saved by Synaptoscope still open.
5. **Don't rename the folders yourself:** the repository folder, the conda
   environment (`envs\synaptoscope`), `synaptoscope-data` and the vault folder
   `02 - Synaptoscope` stay as they are. Give me the exact steps to rename
   them, and which paths in this prompt, the build script and the tools change
   when I do.

**Done when.** A search for "Synaptoscope" in `app/`, `analysis/`, `export/`
and the docs finds only the compatibility package, the settings migration and
"formerly Synaptoscope" in the About box and manual; an old settings file and
an old project open with nothing missing; the build produces
`Clampwise-windows.zip`.

**Your changes.**
-

## B18. Foundations for scale: data, speed, batches and provenance

**What it is for.** Keep the app fast and trustworthy as the data, the lab
and the list of analyses grow: hundreds of files per experiment, years of
experiments, new recording types and analyses added without rewriting what
works, and any number traceable back to the file and settings that made it.

**The standard.** The analysis cascade as a fixed pipeline: import,
preprocess, analyze, summarize, visualize (Nylen & Wallisch 2017). The file
formats are Clampex's (ABF, as documented by Molecular Devices and read by
pyabf) and WinLTP's; nothing else (see the scope in Part A).

**Requirements.**
1. **One data model.** Recording -> sweeps -> channels, each channel with
   units, sample rate, clamp mode, gain, and the stimulus (epochs, stimulus
   times); the recording with its start time, identity (animal, slice, cell,
   condition) and experiment metadata (age, sex, genotype, internal and
   external solutions, temperature, junction potential, drugs with on and off
   times). The ABF and WinLTP readers fill it; analyses read only it, never
   a file directly.
2. **Two formats, read completely.** Clampex ABF (ABF1 and ABF2, episodic
   and gap-free) and WinLTP files, and nothing else: no general multi-format
   reader layer, no `neo`. Getting these two right matters more than reading
   more: test the readers on files from every Clampex protocol the lab uses
   (current steps, paired pulses, light trains, gap-free, DSI) and on my
   WinLTP experiments.
3. **Load lazily.** Headers first, samples only when needed (memory-mapped
   where the format allows); display decimated by min/max so a 60-min file
   scrolls smoothly; memory use for a 60-min, 20 kHz file stays under a
   budget you propose and test.
4. **One project per experiment.** A project folder holding a SQLite
   database (Python's built-in `sqlite3`) of recordings, identity, settings,
   review decisions (accepted and rejected events, overrides, exclusions with
   their reasons) and an index of results. A lab member who opens my project
   sees what I see.
5. **Cache results** keyed by the file's content hash, the analysis settings
   and the analysis version: changing a setting re-runs only what it affects;
   reopening a project is near-instant; the cache can be cleared.
6. **Provenance on every number:** file and hash, analysis name and version,
   settings, app version and date, carried into Results and every export
   manifest. Re-running the same inputs gives identical numbers.
7. **Batches in the background.** Analyze a folder or a whole project in
   worker processes (standard library `concurrent.futures`), with progress,
   cancel, and a per-file error that never stops the batch; the window stays
   responsive throughout.
8. **Same analyses without the window:** `clampwise analyze <folder>
   --preset DSI` and a Python API (`clampwise.analyze(...)`) give the same
   numbers as the window, checked by a test.
9. **Adding an analysis is one file.** An analysis declares, through
   `addons.register`, the recording types it applies to, its settings (default,
   units, one-line help, source), and its outputs with definitions. The window
   builds the settings panel from that declaration (following B0, with
   "Advanced" for the rest), so a new analysis needs no interface code. Write
   `docs/development/ADDING_AN_ANALYSIS.md` with a worked example and a
   known-answer test template.
10. **Presets** are named, versioned sets of settings for an experiment type
    (my DSI preset, LTP, intrinsic properties, paired pulses), stored in the
    project and printed in exports.
11. **Use everything Clampex records.** The protocol name (to pick the
    preset and the analyses automatically, saying so), the epoch table and
    holding level (the stimulus and the steps, before falling back on the
    command trace, B2), telegraphed gain and clamp mode, the file's start time
    (B1), and the tags and comments typed during recording (as drug on and off
    times and notes, B19). For WinLTP, the same for what its files carry
    (stimulus times, slope and amplitude cursors, the LTP baseline and
    induction marks). When a file is missing something the analysis needs,
    say what and ask for it to be typed in.
12. **Performance budgets checked by tests** in `tools/audit/`: e.g. headers
    of 100 files in under 5 s, scrolling under 0.2 s per frame, an evoked-file
    analysis under 1 s. Propose the numbers from measurements on my files.

**Done when.** A 200-file experiment analyzes in the background with the
window responsive; reopening the project takes under a tenth of the first
run; a command-line run on DSI_data matches the window's numbers exactly; a
toy analysis added as one file appears in the window with its settings panel,
in Results and in the exports, with no interface code written.

**Your changes.**
-

## B19. At the rig: analysis while patching

**What it is for.** Help decide during the experiment, not afterwards: is the
cell healthy, is access holding, did the drug arrive, is the baseline stable,
keep recording or move on.

**The standard.** The membrane test (Axon Guide; Marty & Neher in Sakmann &
Neher); the quality limits in B10; the passive measurements in B20.

**Requirements.**
1. **Rig mode:** a simplified screen (B0, dark theme, large text readable from
   the rig chair) that watches the Clampex folder and analyzes each file with
   the experiment's preset as soon as Clampex closes it (gap-free files while
   they are written).
2. **The cell at a glance:** Rs, Rm, Cm and holding current (voltage clamp) or
   resting potential and bridge balance (current clamp), and noise, plotted
   against time since break-in, with the B10 limits drawn; a badge, not a pop
   up, when a limit is crossed or Rs changes by more than 20%.
3. **Stability:** the evoked amplitude or event frequency against time, the
   baseline window and the drug times marked, and a "baseline stable?" check
   with the rule and its source shown (propose the rule from the LTP and
   pharmacology literature; I decide it).
4. **Quick looks in seconds,** using the same code as the full analysis: I-V
   from a step file, f-I and rheobase from a current-step file, PPR from a
   paired-pulse file.
5. **Notes while recording:** typed, timestamped notes (cell, location, drug
   on and off, comments) stored in the project and lined up with the files on
   the cell's clock (B1); tags and comments typed into Clampex during the
   recording are read in as notes and drug times too; drug times feed the
   plots and the figure builder.
6. **Never gets in Clampex's way:** files opened read-only, partly written
   files tolerated, no file locks.

**Done when.** Replaying one of my experiment folders as if live (a tool that
copies its files in with their original timing), the screen updates within
2 s of each file, flags a simulated Rs jump, and holds no lock on any file
(checked on Windows).

**Your changes.**
-

## B20. Passive properties and recording corrections

**What it is for.** Measure the cell's passive properties and the recording's
own errors (series resistance, junction potential, space clamp), and say how
much they could have changed each number.

**The standard.**
- Membrane test from the capacitive transient of a test pulse: Rs from the
  transient (peak, or an exponential fit extrapolated to the step), Cm from
  the charge or the time constant, Rm from the steady state (Axon Guide; Gillis
  in Sakmann & Neher).
- Capacitance depends on how it is measured in neurons that are not
  isopotential (Golowasch et al. 2009).
- Liquid junction potential from the solutions (Neher 1992; Barry 1994,
  JPCalc).
- Voltage error from uncompensated series resistance (I x Rs) and the clamp's
  time constant (Rs x Cm) (Axon Guide; Sigworth in Sakmann & Neher); offline
  correction (Traynelis 1998) *(verify)*.
- Space clamp: distal inputs are filtered and under-clamped in large neurons
  (Williams & Mitchell 2008; Johnston & Wu; Koch).

**Requirements.**
1. **Test-pulse analysis on every sweep that has one** (found from the
   command), per sweep: Rs, Rm, Cm, tau and holding current, the method named,
   the fit drawn (B21). Feeds B10 and B19.
2. **Capacitance says how it was measured**; where current-clamp and
   voltage-clamp estimates both exist, show both.
3. **Junction potential calculator** from the solutions typed in the project
   (ion concentrations, generalized Henderson equation as in JPCalc). Voltages
   are corrected only when the user turns it on, and every exported voltage
   says whether it was corrected and by how much.
4. **Series-resistance error for each voltage-clamp measurement:** the
   estimated voltage error and the clamp time constant (with the compensation
   typed in, or read from the file when it is recorded there); a flag when the
   error or the time constant is large enough to matter for that measure
   (propose limits with sources; I decide them). Offline Rs correction as an
   option, never the default.
5. **Bridge balance in current clamp:** detect an instant voltage jump at a
   step's onset and flag it.
6. **Space clamp** is explained in the manual and in tooltips on kinetics of
   voltage-clamped synaptic currents in CA3 pyramidal cells; no automatic
   correction.

**Done when.** On simulated cells with known Rs, Rm and Cm (`simulate.py`)
the values come back within 5%; on a lab file they agree with Clampex's
membrane test within a stated tolerance; the junction potential for my
internal solution matches JPCalc (I give you JPCalc's number).

**Your changes.**
-

## B21. Fitting and signal processing: one set of shared tools

**What it is for.** Every analysis filters, takes baselines, averages and
fits curves. Done once, tested once, and described the same way everywhere,
the same operation gives the same answer on every screen.

**The standard.** Fitting, weighting, model comparison and parameter
confidence intervals (Motulsky & Christopoulos 2004); fitting electrical
records (Colquhoun & Sigworth in Sakmann & Neher); filters and sampling (Axon
Guide; Cohen 2014).

**Requirements.**
1. **Filters in one module** (Bessel, Gaussian, Butterworth; low, high,
   band-pass; optional 60 Hz notch with harmonics): type, order, cutoff and
   whether zero-phase are listed with every result and export; a warning when
   a cutoff is at or above the Nyquist frequency or above the acquisition
   filter; the raw data are never changed.
2. **Baselines** are named windows drawn on the trace; drift removal
   (a straight-line fit over a stated window) is optional and stated.
3. **Fits:** one and two exponentials, Boltzmann, Hill, straight line, and
   the synaptic waveform (difference of exponentials). Each returns parameters
   with 95% confidence intervals, residuals, goodness of fit, the fit window,
   and a flag when the fit failed or a parameter sits on a bound. One versus
   two exponentials is chosen by an F test or AICc, and the choice is shown.
   Starting values come from the data. Every fit is drawn over the data.
4. **Kinetics defined in one place** (rise 10-90% or 20-80%, decay tau,
   half-width, latency and how onset is found), and the definitions feed the
   column definitions in `export/definitions.py`.
5. **Averaging** says what it aligns on (stimulus, event onset, peak or
   half-rise) and which sweeps or events went in.
6. Each tool is tested against analytic answers.

**Done when.** No analysis has its own filter or fitting code (a search
shows it); known-answer tests recover fit parameters within tolerance at the
noise of real recordings; every Results table lists the filters and fit
models used.

**Your changes.**
-

## B22. Quantal and fluctuation analysis

**What it is for.** Find out where a change in synaptic strength happens:
the number of release sites, the release probability, the size of one
quantum, and the conductance of single channels, from the variability of
evoked and spontaneous responses.

**The standard.** Quantal analysis (Johnston & Wu); CV analysis (Faber & Korn
1991, already in B3); variance-mean / multiple-probability fluctuation
analysis (Silver 2003; Clements 2003 *(verify)*), with the variance's own error
(Saviane & Silver 2006); non-stationary and peak-scaled fluctuation analysis
(Sigworth 1980; Traynelis et al. 1993 *(verify)*).

**Requirements.**
1. **CV analysis** across conditions: the normalized 1/CV² against mean
   plot, with a plain guide to reading it and its assumptions.
2. **Variance-mean analysis** across conditions with different release
   probability: a parabola fitted with the right weighting, giving N, q and
   release probability with confidence intervals. It needs at least three
   conditions and stable responses; check for a trend in amplitude over the
   sweeps first, and say when the data cannot support it.
3. **Peak-scaled fluctuation analysis** of accepted spontaneous events (B4):
   unitary current, and conductance using the driving force (the reversal
   potential typed in or from B8).
4. Each states its assumptions and flags when they are broken (trend over
   time, too few sweeps, poor fit).

**Done when.** On simulated binomial synapses with known N, p and q, and
simulated channels with a known unitary current, the estimates fall within
their confidence intervals.

**Your changes.**
-

## B23. Statistics at the right level

**What it is for.** Summaries and comparisons that respect how the data are
nested (events in sweeps, sweeps in recordings, recordings in cells, cells in
animals), so the app never makes an effect look surer than it is.

**The standard.** Pseudoreplication (Aarts et al. 2014, in B12); the
hierarchical bootstrap (Saravanan, Berman & Sober 2020); estimation with
uncertainty and the bootstrap (Kass, Eden & Brown 2014); effect sizes with
confidence intervals (Ho et al. 2019; Cumming 2014); which test for which
design (Motulsky).

**Requirements.**
1. **Every table knows its level** (event, sweep, recording, cell, animal),
   and summaries roll up one level at a time, the rule printed ("mean of each
   cell's median amplitude").
2. **Comparisons default to the effect size with its 95% confidence
   interval,** by bootstrap over cells, or the hierarchical bootstrap over
   animals then cells (numpy only). n cells and n animals are always shown.
3. **Standard tests are there for reviewers** (t test, Mann-Whitney, paired
   t, Wilcoxon, via `scipy.stats`), each labelled with its assumptions and
   level, with any correction for multiple comparisons stated.
4. Mixed-effects models need `statsmodels`, a new dependency: ask me first.
5. **Exclusions are recorded,** with their reason, and shown in figures and
   exports (flags are evidence).
6. **A methods paragraph** is generated for each comparison, ready to edit
   into a paper.

**Done when.** On many simulated nested datasets with no true effect, the
animal-level comparison is falsely significant about 5% of the time, and the
app shows how pooling events inflates that rate (a teaching example for the
manual).

**Your changes.**
-

---

# Part C. How to work (always include)

- When remaking the whole app, work in this order: B17 (rename), B18 and
  B21 (foundations and shared tools), B0 (theme and layout), then the
  analyses (B1-B10, B20, B22, B23), then B19 (rig mode), B11-B15 and B16.
  Foundations first, so nothing is built twice.
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
