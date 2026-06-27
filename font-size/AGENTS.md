# Physical Font Size Calculator

Create a single-file, self-contained interactive web application (`HTML`, `CSS`, and vanilla `JavaScript`) that calculates and previews the precise real-world physical dimension (in millimeters and inches) of a font rendered on a display screen.

## 1. User Input Parameters & Control Flow

* **Operating System:** Dropdown offering **Windows** (96 DPI base environment) and **macOS** (72 DPI base environment).
* **Aspect Ratio:** Dropdown featuring typical display profiles: `16:9` (Standard), `16:10` (Productivity/Mac), `21:9` (Ultrawide), `3:2` (Surface), and `4:3` (Legacy).
* **Monitor Diagonal Size:** Context-aware dropdown that dynamically updates based on the selected aspect ratio, prepopulated with standard industry sizes (e.g., 13.3", 15.6", 24", 27", 34").
* **Screen Resolution:** Context-aware dropdown mapping typical resolutions matched to the chosen aspect ratio (e.g., `1920×1080` or `3840×2160` for 16:9; `2560×1600` or `3456×2234` for 16:10).
* **Font Configuration:** A numeric field paired with an OS-specific unit selection dropdown containing **pt (Points)**, **px (CSS/Logical Pixels)**, and **em**.

## 2. Core Calculation Engine (JavaScript)

The backend logic must instantly calculate metrics upon any input change using the following formulas:

* **Physical Screen Boundaries:** Use trigonometry ($Diagonal \times \cos(\theta)$ and $Diagonal \times \sin(\theta)$ where $\theta = \arctan(HeightRatio / WidthRatio)$) to determine the screen's real physical width and height in inches.
* **Calculated PPI (Pixels Per Inch):** Determine screen density using the Pythagorean theorem:

$$\text{PPI} = \frac{\sqrt{\text{Horizontal Pixels}^2 + \text{Vertical Pixels}^2}}{\text{Diagonal Size in Inches}}$$


* **UI Scaling Estimation:** Automatically estimate default UI scaling thresholds based on PPI brackets (e.g., Windows scaling up to 150%/200% on high-DPI; macOS treating Retina screens with a 2.0x asset scale factor).
* **Unit Conversion:** Map the user’s font input into true physical hardware pixels, properly accounting for the divergent layout pipelines of Windows (96 DPI tracking) vs. Mac (direct 72-point mapping).

## 3. UI, Metrics Display & Live Preview

* **Calculated Metrics Dashboard:** Display a clean grid containing:
* Calculated Display PPI
* Estimated UI Scaling Factor (%)
* Physical Screen Dimensions (Width × Height in inches)
* **Actual physical font height** in millimeters (`mm`) and inches (`"`) rounded to two decimal places.


* **Live Preview Window:** Provide a visual sandbox containing a clean serif character (e.g., "E"). The element's CSS font size must be dynamically bound *directly* to the physical millimeter result using absolute CSS units (`font-size: X.XXmm`), allowing the user to verify accuracy with a real-world ruler against their monitor.
* **Design Styling:** Use a modern, responsive, clean semantic design layout with neutral, professional colors (`#2563eb` primary accent) and a scannable structure. Ensure a mobile-friendly column collapse layout via media queries.
