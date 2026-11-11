# Universal Human Calendar (UHC) 2.0 Converter

A lightweight, browser-based converter between the Gregorian calendar and the proposed **Universal Human Calendar (UHC) 2.0**.

UHC 2.0 uses a 13-month year. Each month has exactly 28 days, with **Leap Day** and **World Day** placed outside the months. Unlike some 13-month calendar proposals, UHC 2.0 keeps the seven-day week continuous: intercalary days count as regular days in the weekday cycle.

> **Status:** UHC is a proposed calendar system, not an official international civil calendar. This project implements the UHC 2.0 rules described below.

## Features

- Convert Gregorian dates to UHC dates and UHC dates back to Gregorian dates.
- Enter Gregorian dates using CE or BCE year notation.
- Enter regular UHC dates, Leap Day, or World Day.
- Convert date and local time together.
- Choose the device timezone, UTC, or a manual UTC offset.
- View the calculated weekday, year length, day-of-year number, and UTC equivalent.
- Copy either the UHC or Gregorian timestamp.
- Reverse the conversion direction while carrying the current result into the other form.
- Responsive interface for desktop and mobile screens.
- Standalone implementation: HTML, CSS, and JavaScript in one file, with no framework, build step, external library, backend, or network request.

## UHC 2.0 calendar rules

### Epoch and year numbering

The UHC epoch is defined as:

**1 January 10,000 BCE = 1 January, Year 1 HE**

The epoch is a fixed mathematical reference using the **proleptic Gregorian calendar**. It is not a claim that civilisation began everywhere on one exact date.

The year conversion is:

| Gregorian year | UHC year |
|---|---:|
| CE year `Y` | `Y + 10000` HE |
| BCE year `Y` | `10001 - Y` HE |

Examples:

- 10,000 BCE → 1 HE
- 1 BCE → 10,000 HE
- 1 CE → 10,001 HE
- 2026 CE → 12,026 HE

UHC has no year zero. Internally, the converter uses astronomical Gregorian year numbering for calculations, where astronomical year `0` corresponds to 1 BCE.

### Months

There are 13 months, each containing exactly 28 days (four seven-day weeks).

| Number | Month | Days |
|---:|---|---:|
| 1 | January | 28 |
| 2 | February | 28 |
| 3 | March | 28 |
| 4 | April | 28 |
| 5 | May | 28 |
| 6 | June | 28 |
| 7 | Sol | 28 |
| 8 | July | 28 |
| 9 | August | 28 |
| 10 | September | 28 |
| 11 | October | 28 |
| 12 | November | 28 |
| 13 | December | 28 |

Sol is placed between June and July.

### Intercalary days

Intercalary days belong to the year but do not belong to any month.

- **Leap Day (`LD`)** occurs in leap years, after June 28 and before Sol 1. Its day-of-year ordinal is 169.
- **World Day (`WD`)** occurs every year, after December 28. It is ordinal 365 in an ordinary year and ordinal 366 in a leap year.

Both days count in the continuous weekday cycle. They do not reset or interrupt the week.

### Leap years

UHC uses the Gregorian 400-year leap-year rule:

1. A year divisible by 400 is a leap year.
2. Otherwise, a year divisible by 100 is not a leap year.
3. Otherwise, a year divisible by 4 is a leap year.
4. All other years are ordinary years.

There are 97 leap years in every 400-year cycle. An ordinary UHC year has 365 days (364 month days + World Day); a leap year has 366 days (364 month days + Leap Day + World Day).

### Weekdays

The weekday sequence is Monday through Sunday and continues uninterrupted across year boundaries and intercalary days. The UHC epoch, 1 January 10,000 BCE, is assigned Monday.

Because World Day and Leap Day count as days in the week, months are regular but the weekday on which a new year begins changes from year to year.

## Date and timestamp notation

Regular UHC dates use:

```text
HEYYYYY-MM-DD
```

The year is padded to at least five digits; month and day are two digits.

Examples:

```text
HE12026-12-04   # 4 November 2026 CE
HE12028-LD      # Leap Day in 12028 HE
HE12026-WD      # World Day in 12026 HE
```

A timestamp appends the local time and UTC offset:

```text
HE12026-12-04T12:30:45+05:30
HE12026-WDT12:30:45Z
```

Gregorian equivalents are displayed in Gregorian date notation. The converter also displays the UTC date and time corresponding to the selected local date, time, and offset.

## Conversion logic

The converter preserves the chronological day and time; it does not simply preserve the Gregorian month and day numbers.

1. Convert the Gregorian year to its corresponding HE year.
2. Calculate the date's ordinal day within the Gregorian year.
3. Identify World Day or Leap Day when the ordinal matches an intercalary position.
4. For other dates, account for Leap Day in leap years and map the remaining ordinal onto the 13 uniform 28-day months.
5. For reverse conversion, calculate the UHC ordinal and resolve it to the corresponding proleptic Gregorian month and day.

For a regular UHC date with month number `m` and day `d`, the ordinal is:

```text
N = 28 × (m - 1) + d
```

In a leap year, add one to `N` for months 7 through 13. Leap Day has ordinal 169; World Day has ordinal 365 or 366 depending on the year.

The weekday is calculated from the number of elapsed days since the epoch, modulo seven, with Monday as index zero.

## Use the converter

### Run locally

1. Download or clone this repository.
2. Open `index.html` in a modern browser.
3. Choose a conversion direction:
   - **Gregorian → UHC** to enter a Gregorian date.
   - **UHC → Gregorian** to enter a UHC date.
4. Enter the date, time, and timezone mode.
5. Select **Convert date & time**. The result panel displays both calendar dates and related time information.

No installation or build process is required. JavaScript must be enabled in the browser.

For a local HTTP preview, you can also run:

```bash
python -m http.server 8000
```

Then open <http://localhost:8000>.

### Timezone options

- **Device timezone** uses the browser's configured local timezone.
- **UTC** treats the entered time as UTC.
- **Manual UTC offset** accepts an offset such as `+05:30`, `-04:00`, or `Z`.

The selected wall-clock time and timezone are retained when converting between calendar systems. The UTC equivalent is calculated and displayed separately. For historical dates, the browser's timezone data may not represent historical local-time practices accurately; use UTC or a known manual offset when exact historical timezone interpretation is required.

## Reference conversion examples

These examples are also checked by the converter's built-in conformance checks.

| Gregorian date | UHC date | Notes |
|---|---|---|
| 1 January 10,000 BCE | `HE00001-01-01` | UHC epoch; Monday |
| 8 November 2026 | `HE12026-12-04` | Sunday |
| 17 June 2028 | `HE12028-LD` | Leap Day |
| 18 June 2028 | `HE12028-07-01` | Sol 1 |

## Project structure

The app is intentionally kept as a single file:

```text
.
├── index.html   # Complete application: markup, styles, and conversion logic
└── README.md    # Project documentation
```

If the downloaded HTML file has another name, rename it to `index.html` for the simplest GitHub Pages setup.

## Deploy with GitHub Pages

This is a static site and does not need a build command.

1. Add the application file to the repository root as `index.html`.
2. Add this `README.md` to the repository root.
3. Push the files to the `main` branch.
4. In the repository, open **Settings → Pages**.
5. Under **Build and deployment**, choose **Deploy from a branch**.
6. Select `main` and the `/ (root)` folder, then save.
7. Open the published URL shown in the Pages settings.

See the official [GitHub Pages guide](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site) and [publishing-source documentation](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site) for current instructions.

## Scope and limitations

- UHC 2.0 is a proposed civil-calendar design; it is not currently an official or universally adopted standard.
- Historical Gregorian conversions use the **proleptic Gregorian calendar**. A historical date originally recorded in the Julian, Hebrew, Islamic, Hindu, or another calendar must first be interpreted in that original calendar before conversion to a Gregorian-equivalent date.
- BCE input uses BCE year notation; internal calculations use astronomical year numbering.
- Device-timezone conversion depends on the browser and its timezone database. For historical dates, local timezone offsets can be uncertain or unavailable; UTC or a manually supplied offset is preferable when precision matters.
- The converter does not calculate astronomical events or dynamically adjust the leap-year rule. It applies the fixed Gregorian 400-year cycle specified by UHC 2.0.
- A date conversion does not change the underlying definition of a second, UTC, Unix time, or any physical time standard.

## Privacy

All conversion logic runs locally in the browser. The application contains no analytics, tracking, backend, or external library calls, and it does not transmit entered dates or times to a server.

## License

No license is currently included. Add a `LICENSE` file to the repository to specify the terms under which others may use, modify, and distribute the project.
