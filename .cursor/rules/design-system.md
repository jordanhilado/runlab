# Design System (`runlab/DesignSystem.swift`)

All tokens, reusable components, and modifiers live in this single file.

## Colors

```swift
extension Color {
    static let brandGreen    = Color(hex: "#A3E635")  // CTAs, positive, PRs
    static let signalTeal    = Color(hex: "#00D68F")  // Fitness, positive trends
    static let cautionYellow = Color(hex: "#FFD60A")  // Fatigue, moderate
    static let alertRed      = Color(hex: "#FF453A")  // Overreaching, errors
    static let bgDark        = Color(hex: "#0D0D0D")  // Primary background
    static let surface       = Color(hex: "#1A1A1A")  // Cards, sheets
    static let surfaceHigh   = Color(hex: "#242424")  // Nested cards, inputs
    static let borderSubtle  = Color(hex: "#2A2A2A")  // Card borders, dividers
    static let textPrimary   = Color(hex: "#FFFFFF")
    static let textSecondary = Color(hex: "#666666")
    static let textTertiary  = Color(hex: "#444444")
}
```

Accent colors appear only for functional signals — never decorative. Default resting state is monochromatic.

## Typography

| Role | Font | Weight | Size | Bundled? |
|------|------|--------|------|----------|
| Display / Hero | Cooper BT | Bold (700) | 40–56pt | Falls back to SF Pro Rounded Bold |
| Headline | Cooper BT | Bold (700) | 24–32pt | Falls back to SF Pro Rounded Bold |
| Section Header | Cooper BT | Medium (500) | 17–20pt | `Cooper-BT-Medium.otf` |
| Body | Manrope | Regular (400) | 15–17pt | `Manrope-Regular.ttf` |
| Caption / Metadata | Manrope | Light (300) | 12–13pt | Falls back to SF Pro Text Light |
| Chart Labels | Readex Pro | Light (300) | 11–13pt | `ReadexPro-Light.ttf` |
| Unit Labels | Readex Pro | Medium (500) | 12–14pt | Falls back to SF Pro Display |
| Metric / Number | Readex Pro | Bold (700) | varies | Falls back to SF Pro Display Bold |

Bundled fonts listed in `UIAppFonts` in `Info.plist`. Use PostScript name via `CTFontCopyPostScriptName`.

## Spacing & Radii

```swift
let spacing4: CGFloat = 4;  let spacing8: CGFloat = 8
let spacing12: CGFloat = 12; let spacing16: CGFloat = 16
let spacing24: CGFloat = 24; let spacing32: CGFloat = 32
let spacing48: CGFloat = 48

let radiusSM: CGFloat = 10;  let radiusMD: CGFloat = 16
let radiusLG: CGFloat = 20;  let radiusXL: CGFloat = 28
let radiusPill: CGFloat = 999
```

## Reusable Components

- **`CardModifier`** — `surface` bg, `radiusMD`, 1pt `borderSubtle` stroke. Applied via `.cardStyle()`.
- **`PrimaryButton`** — `brandGreen` fill, black Manrope SemiBold label, 50pt height, `radiusPill`, 44pt min touch.
- **`SecondaryButton / Badge`** — `surfaceHigh` bg, `borderSubtle` 1pt stroke, white label, `radiusPill`.
- **`MetricDisplay`** — Readex Pro Bold number, Readex Pro Regular `textSecondary` caption above.
- **`ShimmerModifier`** — `.shimmer(isLoading: Bool)` animated LinearGradient skeleton.
- **`ProGate`** — `.proGate(feature:)` ViewModifier: blurs content, shows upgrade CTA for non-Pro.
- **Section Headers** — `textSecondary`, Readex Pro Medium, uppercase, letter-spacing +1pt.
- **Status Pills** — `surfaceHigh` bg, `radiusPill`, white Manrope Medium 13pt.
