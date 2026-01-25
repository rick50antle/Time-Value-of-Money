# Tutorial Development Standards

**Yale School of Management - ACCT 2700: Foundations of Accounting & Valuation**

This document outlines the standards and conventions for developing tutorials in the Time Value of Money series. All tutorials should follow these guidelines for consistency.

---

## 1. File Structure

### Naming Convention
- Main tutorials: `Tutorial-[Letter]-[Name].html` (e.g., `Tutorial-A-ThePuzzle.html`, `Tutorial-B-TheMachine.html`)
- Deep Dives: `Tutorial-DeepDive-[Name].html` (e.g., `Tutorial-DeepDive-Pitfalls.html`)

### Required Constants
Every tutorial should define:
- `COLORS` object with consistent color palette
- `PARTS` array defining tutorial structure with `{ id, title, steps }`
- `RABBIT_HOLES` object (if applicable)
- `GLOSSARY` object with relevant terms

---

## 2. Header Component

### Required Elements (in order)
1. **Title** - Tutorial name (e.g., "Tutorial B: The Machine" or "Bond Pricing")
2. **Sound Toggle** - Button with Volume2/VolumeX icons
3. **Rabbit Hole Counter** - Clickable button showing explored/total count
4. **Glossary Button** - Must include both icon AND "Glossary" text label
5. **Restart Button** - RotateCcw icon

### Glossary Button Standard
The Glossary button must always include both the icon and text label:
```jsx
<button onClick={onGlossaryClick} style={{ /* ... */ }}>
  <LucideIcon name="BookOpen" size={16} />
  Glossary
</button>
```

### Rabbit Hole Counter (Clickable)
The rabbit hole counter must be a clickable button that opens the Rabbit Hole List Modal:
```jsx
<button
  onClick={onRabbitHoleListClick}
  style={{
    display: 'flex',
    alignItems: 'center',
    gap: '4px',
    background: COLORS.rabbitHoleLight,
    color: COLORS.rabbitHole,
    padding: '4px 10px',
    borderRadius: '12px',
    fontSize: '13px',
    fontWeight: '500',
    border: 'none',
    cursor: 'pointer',
    transition: 'all 0.2s ease',
  }}
  title="View all rabbit holes"
>
  <LucideIcon name="Rabbit" size={14} />
  <span>{exploredRabbitHoles.length}/{totalRabbitHoles}</span>
</button>
```

---

## 3. Rabbit Holes vs Deep Dives

### Rabbit Holes
- **Purpose**: Short, optional asides that enrich but don't interrupt the flow
- **Presentation**: Modal overlay
- **Duration**: Brief reads (1-3 minutes)
- **Placement**: Inline within tutorial steps
- **Visual**: Dashed purple border, Rabbit icon
- **Behavior**: User stays in tutorial flow

```jsx
<RabbitHoleButton
  rabbitHole={RABBIT_HOLES.topicName}
  explored={exploredRabbitHoles.includes('topicName')}
  onClick={() => handleRabbitHoleClick('topicName')}
  soundEnabled={soundEnabled}
/>
```

### Deep Dives
- **Purpose**: Full mini-tutorials that explore topics in depth
- **Presentation**: Separate page (navigation away from current tutorial)
- **Duration**: Extended learning (5-15 minutes)
- **Placement**: At natural breakpoints with "when you're ready" framing
- **Visual**: Teal color scheme, Layers icon, subtle border

```jsx
const DeepDiveLink = ({ title, href }) => (
  <a
    href={href}
    style={{
      display: 'flex',
      alignItems: 'center',
      gap: '10px',
      padding: '12px 16px',
      border: `1px solid ${COLORS.deepDiveBorder}`,
      borderRadius: '8px',
      background: COLORS.deepDiveLight,
      color: COLORS.deepDive,
      textDecoration: 'none',
      fontSize: '14px',
      marginTop: '16px',
    }}
  >
    <LucideIcon name="Layers" size={18} style={{ flexShrink: 0 }} />
    <span>For more on this, see the Deep Dive: <strong>{title}</strong></span>
  </a>
);
```

### Deep Dive Colors (add to COLORS object)
```javascript
deepDive: '#0d9488',        // teal-600
deepDiveLight: '#f0fdfa',   // teal-50
deepDiveBorder: '#5eead4',  // teal-300
```

---

## 4. Rabbit Hole List Modal

Every tutorial with rabbit holes must include a `RabbitHoleListModal` component that displays all available rabbit holes when the counter is clicked:

```jsx
const RabbitHoleListModal = ({ exploredRabbitHoles, onSelectRabbitHole, onClose }) => (
  <div style={{
    position: 'fixed',
    inset: 0,
    background: 'rgba(0,0,0,0.5)',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    padding: '20px',
    zIndex: 1000,
  }}>
    <div style={{
      background: 'white',
      borderRadius: '16px',
      maxWidth: '500px',
      width: '100%',
      maxHeight: '80vh',
      overflow: 'auto',
      boxShadow: '0 20px 60px rgba(0,0,0,0.3)',
    }}>
      {/* Header */}
      <div style={{
        background: COLORS.rabbitHole,
        color: 'white',
        padding: '20px 24px',
        display: 'flex',
        alignItems: 'center',
        gap: '12px',
      }}>
        <LucideIcon name="Rabbit" size={24} />
        <h2 style={{ margin: 0, fontSize: '18px', fontWeight: '600' }}>
          Rabbit Holes
        </h2>
        <span style={{
          marginLeft: 'auto',
          background: 'rgba(255,255,255,0.2)',
          padding: '4px 10px',
          borderRadius: '12px',
          fontSize: '13px',
        }}>
          {exploredRabbitHoles.length}/{Object.keys(RABBIT_HOLES).length} explored
        </span>
      </div>

      {/* Content - list of rabbit hole buttons */}
      <div style={{ padding: '16px' }}>
        {Object.values(RABBIT_HOLES).map((rabbitHole) => {
          const isExplored = exploredRabbitHoles.includes(rabbitHole.id);
          return (
            <button
              key={rabbitHole.id}
              onClick={() => onSelectRabbitHole(rabbitHole)}
              style={{
                display: 'flex',
                alignItems: 'center',
                gap: '12px',
                width: '100%',
                padding: '14px 16px',
                marginBottom: '8px',
                border: `2px solid ${isExplored ? COLORS.rabbitHoleBorder : COLORS.border}`,
                borderRadius: '10px',
                background: isExplored ? COLORS.rabbitHoleLight : 'white',
                color: COLORS.textPrimary,
                cursor: 'pointer',
                textAlign: 'left',
                transition: 'all 0.2s ease',
              }}
            >
              <LucideIcon name="Rabbit" size={20} color={isExplored ? COLORS.rabbitHole : COLORS.textMuted} />
              <span style={{ flex: 1, fontWeight: '500' }}>{rabbitHole.title}</span>
              {isExplored && <LucideIcon name="Check" size={18} color={COLORS.rabbitHole} />}
            </button>
          );
        })}
      </div>

      {/* Footer */}
      <div style={{
        padding: '16px 24px',
        borderTop: `1px solid ${COLORS.border}`,
        display: 'flex',
        justifyContent: 'flex-end',
      }}>
        <button onClick={onClose} style={{
          padding: '10px 24px',
          border: 'none',
          borderRadius: '8px',
          background: COLORS.rabbitHole,
          color: 'white',
          fontSize: '15px',
          fontWeight: '500',
          cursor: 'pointer',
        }}>
          Close
        </button>
      </div>
    </div>
  </div>
);
```

### Required State and Handlers
```javascript
const [showRabbitHoleList, setShowRabbitHoleList] = useState(false);

const handleRabbitHoleFromList = useCallback((rabbitHole) => {
  setShowRabbitHoleList(false);
  setActiveRabbitHole(rabbitHole);
  if (!exploredRabbitHoles.includes(rabbitHole.id)) {
    setExploredRabbitHoles(prev => [...prev, rabbitHole.id]);
  }
}, [exploredRabbitHoles]);
```

---

## 5. Cash Flow Diagrams

### Timeline Alignment
When comparing multiple cash flow streams, both diagrams must show the same timeline:

**Correct:**
- Stream A: Time 0, Time 1, Time 2, Time 3 (with zero amounts where applicable)
- Stream B: Time 0, Time 1, Time 2, Time 3

**Incorrect:**
- Stream A: Time 0 only
- Stream B: Time 0, Time 1, Time 2, Time 3

```jsx
// If Stream A has a single payment at Time 0, still show the full timeline:
<CashFlowDiagram
  flows={[
    { period: 0, amount: 83.96 },
    { period: 1, amount: 0 },
    { period: 2, amount: 0 },
    { period: 3, amount: 0 }
  ]}
  highlightPeriod={0}
  label="Stream A"
/>
```

---

## 6. Color Palette (Standard)

```javascript
const COLORS = {
  // Primary (Yale Blue)
  primary: '#00356b',
  primaryLight: '#1a4a7a',
  primaryDark: '#002548',

  // Surface & Background
  background: '#f8fafc',
  surface: '#ffffff',
  surfaceAlt: '#f1f5f9',

  // Borders
  border: '#e2e8f0',
  borderDark: '#cbd5e1',

  // Text
  textPrimary: '#1e293b',
  textSecondary: '#64748b',
  textMuted: '#94a3b8',

  // Accent (Blue)
  accent: '#0369a1',
  accentLight: '#e0f2fe',

  // Gold (Insights)
  gold: '#ca8a04',
  goldLight: '#fef9c3',
  goldBorder: '#eab308',

  // Success
  success: '#16a34a',
  successBg: '#f0fdf4',
  successBorder: '#86efac',

  // Warning
  warning: '#d97706',
  warningBg: '#fffbeb',
  warningBorder: '#fcd34d',

  // Error
  error: '#dc2626',
  errorBg: '#fef2f2',

  // Rabbit Holes (Purple)
  rabbitHole: '#7c3aed',
  rabbitHoleLight: '#ede9fe',
  rabbitHoleBorder: '#a78bfa',

  // Deep Dives (Teal)
  deepDive: '#0d9488',
  deepDiveLight: '#f0fdfa',
  deepDiveBorder: '#5eead4',
};
```

---

## 7. Terminology

### Coupon Rate vs Interest Rate
- **Coupon Rate**: Fixed contractual language describing promised cash flows (never changes)
- **Interest Rate / Discount Rate**: Market price of borrowing through time (changes constantly)

The tutorials emphasize this distinction is critical and often confused by students.

### Bond Pricing Terms
- **At Par**: Price equals face value (coupon rate = interest rate)
- **At Discount**: Price below face value (interest rate > coupon rate)
- **At Premium**: Price above face value (interest rate < coupon rate)

---

## 8. Pedagogical Guidelines

### Flow and Transitions
- Avoid abrupt transitions between sections
- Use bridge text to connect concepts smoothly
- Example: "This personal nature of value has to be overcome before we have any chance at measurement."

### Question Framing
- Prefer value-based questions over preference questions
- "Which is worth more?" rather than "Which would you prefer?"
- This emphasizes that the puzzle is about value, not personal preference

### Text Box Consistency
- Concluding text boxes should create engagement, not premature closure
- Example ending: "That's what we're here to figure out." (invites exploration)
- Avoid: "That's less obvious." (flat ending)

---

## 9. Dev Mode

All tutorials should include a dev mode panel for testing, activated by triple-clicking the header title:

```javascript
const [devMode, setDevMode] = useState(false);
const [headerClickCount, setHeaderClickCount] = useState(0);
const [lastClickTime, setLastClickTime] = useState(0);

const handleHeaderClick = useCallback(() => {
  const now = Date.now();
  if (now - lastClickTime < 500) {
    const newCount = headerClickCount + 1;
    setHeaderClickCount(newCount);
    if (newCount >= 3) {
      setDevMode(prev => !prev);
      setHeaderClickCount(0);
    }
  } else {
    setHeaderClickCount(1);
  }
  setLastClickTime(now);
}, [headerClickCount, lastClickTime]);
```

---

## 10. Sound System

All tutorials use consistent sound effects:
- `click` - Button interactions
- `success` - Correct answers
- `softNope` - Incorrect answers
- `rabbitHole` - Opening rabbit holes
- `insight` - Key realizations
- `celebration` - Tutorial completion

Sound can be toggled on/off via the header button.

---

*Last Updated: January 2026*
