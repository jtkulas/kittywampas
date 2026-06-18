**Here are several better alternatives to `TeachingDemos::cal()` for embedding calendars in your Quarto site.** These options improve on static base-R plots by offering better customization, ggplot2 integration (which fits your R dataviz workflow), interactivity, or easier event highlighting.

### 1. calendR (Best Direct Replacement – ggplot2-Based)
The **`calendR`** package is the most straightforward upgrade. It creates fully customizable, ready-to-print monthly or yearly calendars with ggplot2, including support for events, colors, text labels, moon phases, and even calendar heatmaps.

**Installation and basic use:**
```r
# install.packages("calendR")
library(calendR)

# Yearly calendar
calendR(year = 2026)

# Monthly with events and custom styling
calendR(
  year = 2026, month = 6,
  start = "M",               # Monday start
  special.days = c(10, 15, 20),  # Highlight days
  special.col = "lightblue",
  title.size = 20,
  text.size = 10
)

# Calendar heatmap (e.g., for activity levels)
calendR(year = 2026, 
        special.days = your_event_dates,
        special.col = scales::viridis_pal()(10))
```

**In Quarto:** Chunk outputs are high-quality ggplot2 plots (SVG/PNG). Add `fig-width`, `fig-height`, and themes for polish. You can script events from a data frame (e.g., CSV or tibble) for dynamic updates on re-render.

This keeps everything in R/ggplot2 while looking far more professional than `TeachingDemos`.

### 2. Custom ggplot2 Calendars (Flexible, No Extra Package Needed)
Build your own with `ggplot2` + `lubridate`/`dplyr` for full control. Packages like **sugrrants** (`frame_calendar()`) or simple grid setups also work well.

Example skeleton:
```r
library(tidyverse)
library(lubridate)

dates <- tibble(date = seq(as.Date("2026-01-01"), as.Date("2026-12-31"), by = "day")) %>%
  mutate(weekday = wday(date, label = TRUE),
         month = month(date, label = TRUE),
         day = day(date),
         week = floor((yday(date) - 1) / 7) + 1)  # etc.

ggplot(dates, aes(x = weekday, y = -week, label = day, fill = some_value)) +
  geom_tile(color = "white") +
  geom_text() +
  facet_wrap(~ month) +
  scale_fill_viridis_c() +  # for heatmaps
  theme_minimal()
```

This integrates seamlessly with your existing dataviz code (e.g., overlay event data or metrics).

### 3. Interactive JavaScript Calendars (via Quarto HTML Features)
For a more modern, clickable experience (e.g., event popups, navigation), embed JS libraries. Quarto HTML supports this natively.

- **FullCalendar** (most popular): Highly performant, supports events, drag-and-drop, multiple views. Include via HTML/JS in a Quarto page or use Observable JS.
  - Add to your `.qmd` with `include-before-body` or raw HTML blocks.
  - Example resources exist for Quarto + ICS files or static event JSON.

- **Observable JS** (built into Quarto): Use reactive JS calendars or D3-based ones. Great if you want interactivity tied to R data (e.g., pass events via `ojs_define()`).

**Simple embed example (FullCalendar):**
```yaml
# In YAML or include-in-header
include-in-header:
  text: |
    <link href='https://cdn.jsdelivr.net/npm/fullcalendar@6/index.global.min.css' rel='stylesheet' />
    <script src='https://cdn.jsdelivr.net/npm/fullcalendar@6/index.global.min.js'></script>
```

Then a `{html}` or raw block with a `<div id='calendar'></div>` and script to initialize with your events.

### 4. Other R Options
- **calendar** package: For generating/reading `.ics` files (iCalendar format). Export events and display via JS above.
- Heatmap-focused: `openair::calendarPlot()` or custom ggplot2 for activity tracking.

### Recommendations for Your Site
- **Stay in R**: Start with **calendR** — it's the least friction and produces nicer static output than `TeachingDemos`.
- **Add polish**: Use `ggsave()` for consistent sizing or Quarto figure options.
- **Go dynamic**: If you have recurring/upcoming events, maintain them in a data frame/CSV and script highlights.
- **Interactive upgrade**: Combine R (generate event JSON) + FullCalendar/Observable for a web-native feel.

You can regenerate the page on schedule (e.g., via GitHub Actions or Posit Connect) to keep "Upcoming events as of [date]" fresh.

If you share more details (e.g., how events are stored or desired features like heatmaps/recurrence), I can provide exact code snippets!