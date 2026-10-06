# Comprehensive Technical Specification & Agent Prompt: Star Trek Mobile Bridge Simulator

**Target Output:** Single, self-contained `index.html` file (HTML5 + CSS3 + Vanilla JavaScript + Web Audio API).  
**Target Environment:** Mobile browsers (iOS Safari, Android Chrome) and desktop web views.  
**Zero External Dependencies:** No external CDNs, images, sound files, or frameworks. Everything must be synthesized, rendered via Canvas/SVG/CSS, and run directly offline or via `file://`.

---

## 1. Executive Summary & Vision

The client requires a responsive, touch-optimized, mobile-first Star Trek bridge simulation and tactical game styled after the iconic **LCARS (Library Computer Access and Retrieval System)** interface. 

The player assumes command of a Federation starship (e.g., *USS Enterprise* or *USS Defiant* archetype) patrolling the neutral frontier. Gameplay balances real-time power management, tactical spatial combat, long-range sector exploration, and randomized planetary/anomaly encounters.

### High-Level Pillars
1. **Authentic LCARS Aesthetic:** Asymmetrical pill buttons, elbow brackets, high-contrast flat colors (amber, lilac, cyan, crimson), and crisp monospace readouts.
2. **Ergonomic Mobile UX:** Optimized for one-thumb and two-thumb operation in portrait (with seamless landscape support), meeting standard touch target sizes ($\ge 44 \times 44\text{ px}$).
3. **Pure Single-File Architecture:** All styles, game logic, Canvas rendering, procedural generation, and audio synthesis (Web Audio API) must reside strictly within a single `index.html`.

---

## 2. Technical Constraints & Architecture

The AI coding agent must strictly adhere to the following architecture rules:

| Constraint | Implementation Rule |
| :--- | :--- |
| **File Format** | Single file named `index.html`. |
| **Styling** | Embedded `<style>` tag. No Tailwind CDN, no external font stylesheets (use robust system fallbacks like `system-ui`, `Trebuchet MS`, `Impact`, `Arial Black`). |
| **Scripting** | Embedded `<script>` tag. Modular, object-oriented or functional state machine in vanilla ES6+. |
| **Graphics** | HTML5 `<canvas>` for space combat and tactical star maps; inline SVG and pure CSS for LCARS structural elements. |
| **Audio** | Pure browser `AudioContext` (Web Audio API) generating custom sound effects (beeps, chirps, phasers, torpedoes, and red alert klaxons). Must handle the mobile audio-unlock policy on first user gesture. |
| **Persistence** | Browser `localStorage` for high scores, mission logs, and automatic game saves. |
| **Mobile Viewport** | Bound strictly to `100dvh` (dynamic viewport height) with `overscroll-behavior: none` and `touch-action: manipulation` to prevent pull-to-refresh or accidental zoom. |

---

## 3. Core Gameplay Systems & Mechanics

### 3.1 Ship Subsystem Power Distribution
The ship has a master warp core output (e.g., 100 Power Units) that the player dynamically routes across four essential subsystems using LCARS sliders or tap incrementors:
* **Engines (ENG):** Determines impulse speed, warp recharge rate, and evasion probability against enemy fire.
* **Shields (SHD):** Regulates shield recharge rate and maximum shield deflection capacity (divided into Forward, Aft, Port, Starboard, or a unified frequency barrier).
* **Weapons (WEAP):** Governs phaser beam recharge speed, phaser damage output, and torpedo launcher loading cycle.
* **Sensors & Life Support (SENS):** Sensor sweep radius on the sector map, target lock accuracy, and crew casualty mitigation.
* **Power Overload/Deficit Rule:** If total allocation exceeds core output, subsystem efficiency drops exponentially and hull stress increases.

### 3.2 Tactical Combat System
Combat takes place on a real-time tactical grid or 2D overhead canvas:
* **Phasers:** Continuous energy beam weapon. Requires line-of-sight and direct firing arc. Depletes weapons capacitor. High damage against unshielded targets; moderate against shields.
* **Photon Torpedoes:** Finite ordnance (e.g., 10 torpedoes standard). High kinetic/explosive burst damage. Can be fired in spreads.
* **Deflector Shields:** Absorb kinetic and energy damage. When shields drop to 0%, incoming hits deal structural hull breaches and damage subsystems (disabling engines, weapons, or sensors).
* **Evasion & Firing Arcs:** Tactical maneuvering allows the player to rotate their ship to bring forward torpedo tubes to bear or protect a compromised shield quadrant.

### 3.3 Enemy AI Archetypes
The agent must implement at least three procedurally encountered hostile vessels:
1. **Klingon Bird-of-Prey:** Aggressive, utilizes cloaking field (disappears from sensors for 4–8 seconds), decloaks in flank positions to fire torpedo salvos.
2. **Romulan Warbird:** Heavy plasma torpedoes, high shield capacity, slow turning speed, sustained beam pressure.
3. **Borg Scout / Probe:** Adapts to weapon frequencies. After 3 phaser hits, phaser effectiveness drops by 50% until the player recalibrates shield/phaser modulation via an LCARS command button.

### 3.4 Sector Exploration & Stardate Clock
* The game world consists of an $8 \times 8$ Quadrant Grid (or continuous sector map).
* Sectors contain: Friendly Starbases (repair/rearm), Class-M Planets (away mission events), Stellar Anomalies (research for crew XP/science points), and Hostile Fleets.
* **Stardate Counter:** Advances with every warp jump and sub-light impulse travel. High-score evaluation is based on stardates survived, missions completed, and sectors secured.

---

## 4. UI/UX Specifications (LCARS Design System)

### 4.1 LCARS Color Palette
Define and use standard CSS variables matching authentic Federation LCARS standards:

```css
:root {
  --lcars-bg: #000000;
  --lcars-orange: #FF9900;
  --lcars-amber: #FF7700;
  --lcars-peach: #FF9966;
  --lcars-lilac: #CC99CC;
  --lcars-purple: #996699;
  --lcars-blue: #99CCFF;
  --lcars-cyan: #3399CC;
  --lcars-red: #CC0000;
  --lcars-yellow: #FFFF66;
  --lcars-gray: #444455;
  --lcars-font: -apple-system, BlinkMacSystemFont, "Antonio", "League Gothic", "Impact", "Trebuchet MS", sans-serif;
}
```

### 4.2 Mobile Screen Structure (Portrait Optimized)
The UI must be split into three stacked functional areas:

```
+-------------------------------------------------------+
| TOP HEADER: LCARS Elbow Bracket & Status Readouts     |
| Stardate: 47214.3 | Alert: GREEN/YELLOW/RED | Hull: 98%|
+-------------------------------------------------------+
| MAIN VIEWPORT (Canvas / Dynamic Display)              |
|                                                       |
|  [ Tactical Space Grid / Sensor Map / Planetary Log ]  |
|                                                       |
+-------------------------------------------------------+
| LCARS NAVIGATION PILLS (Tab Switcher)                 |
| [ HELM ]  [ TACTICAL ]  [ ENGINEERING ]  [ SENSORS ]  |
+-------------------------------------------------------+
| LOWER CONSOLE: Context-Sensitive Action Buttons       |
| - Big touch targets (min 48px height)                 |
| - Slider power bars, Fire Phasers, Launch Torpedo     |
| - Warp Jump, Hail, Modulate Shields                   |
+-------------------------------------------------------+
```

### 4.3 Mobile Ergonomics & Touch Guidelines
1. **Thumb-Zone Placement:** High-frequency tactical buttons (Fire Phasers, Fire Torpedo, Shield Boost) must be docked within the bottom 35% of the screen.
2. **Haptic Feedback:** Integrate `navigator.vibrate([15])` on button presses, `[40, 30, 40]` on hull impacts, and `[80]` on critical alarms (guarded by user toggle).
3. **No Double-Tap Zoom:** Add `touch-action: manipulation;` globally to avoid touch delay and unintended zooming.

---

## 5. Web Audio API Sound Synthesizer Specifications

Do not load MP3/WAV files. Create an inline `SoundController` class utilizing `AudioContext` oscillators and gain nodes:

### Audio SFX Recipes
* **LCARS Affirmative Chirp:** Dual-tone oscillator. Frequency 1: $1200\text{ Hz}$, Frequency 2: $1800\text{ Hz}$. Duration: $0.06\text{ s}$. Fast exponential ramp-down.
* **Phaser Beam:** Continuous sawtooth wave modulated with a low-frequency oscillator ($40\text{ Hz}$ vibrato) sweeping from $880\text{ Hz}$ down to $650\text{ Hz}$ with slight white noise buffer.
* **Photon Torpedo Fire:** Quick resonant bandpass burst + descending sine frequency from $400\text{ Hz} \to 80\text{ Hz}$ with rapid attack and exponential decay over $0.4\text{ s}$.
* **Explosion / Impact:** White noise buffer fed into a low-pass filter falling from $800\text{ Hz} \to 40\text{ Hz}$ over $1.2\text{ s}$.
* **Red Alert Klaxon:** Two-tone siren alternating between $440\text{ Hz}$ and $660\text{ Hz}$ every $0.65\text{ s}$ synchronized with a pulsing red UI border.

---

## 6. Development Phases & Priority Matrix

When coding the solution, execute in strictly ordered phases to guarantee a working build at every stage:

```
[Phase 1: Core Shell & Engine] ───► [Phase 2: Power & Tactical Grid]
                │                                    │
                ▼                                    ▼
[Phase 3: Hostile AI & Combat] ───► [Phase 4: Audio & Mobile Polish]
```

### Phase 1: Core Shell, LCARS Layout & State Machine (P0 - Critical)
- [ ] Implement responsive HTML/CSS skeleton with authentic LCARS headers, side elbows, and lower dock.
- [ ] Initialize global `GameState` object (ship coordinates, resources, shields, hull, power, alert level).
- [ ] Implement screen navigation tabs: **HELM**, **TACTICAL**, **ENGINEERING**, **SENSORS**.
- [ ] Implement window resize handling and `100dvh` CSS viewport fixing.

### Phase 2: Power Routing & Tactical Canvas (P0 - Critical)
- [ ] Create interactive power routing system with responsive touch sliders or +/- controls.
- [ ] Implement the tactical `<canvas>` view showing the player's ship at center, heading indicator, and local sector entities.
- [ ] Implement touch drag/joystick or direction taps for ship navigation and sub-light impulse movement.

### Phase 3: Tactical Combat, Weapons & Hostile AI (P1 - High)
- [ ] Implement Phaser targeting ray and Photon Torpedo trajectory physics on the Canvas.
- [ ] Implement enemy spawn logic, patrol movement, and firing routines.
- [ ] Build shield collision calculation and damage dispersion (forward, aft, port, starboard shields).
- [ ] Implement dynamic alert status switching:
  - **Condition Green:** Normal operations.
  - **Condition Yellow:** Hostiles on long-range sensors; power diverted automatically.
  - **Condition Red:** Active combat engagement; red visual strobes and klaxon.

### Phase 4: Exploration, Encounters & Audio Engine (P1 - High)
- [ ] Implement sector jump/warp menu with an $8 \times 8$ quadrant map.
- [ ] Build interactive modal dialogs for away missions, distress hails, and anomaly research with multiple-choice tactical decisions.
- [ ] Implement the complete `SoundController` using vanilla Web Audio API.
- [ ] Add touch audio unlock prompt on initial user interaction.

### Phase 5: Mobile Hardening, Polish & Deployment Readiness (P2 - Medium)
- [ ] Test viewport on iOS Safari (notch/home indicator safe areas using `env(safe-area-inset-*)`).
- [ ] Add `localStorage` persistence for high scores, statistics, and game state resumption.
- [ ] Add scanline CRT visual toggle and low-battery/performance throttling mode.

---

## 7. Concrete Implementation Examples for the Agent

To eliminate hallucination and ensure high fidelity, the coding agent must reference and utilize the following verified patterns:

### 7.1 Web Audio Synthesizer Implementation
```javascript
class LCARSFX {
  constructor() {
    this.ctx = null;
    this.muted = false;
  }

  init() {
    if (!this.ctx) {
      const AudioCtx = window.AudioContext || window.webkitAudioContext;
      this.ctx = new AudioCtx();
    }
    if (this.ctx.state === 'suspended') {
      this.ctx.resume();
    }
  }

  playBeep(freq = 1400, duration = 0.08) {
    if (this.muted || !this.ctx) return;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    osc.type = 'sine';
    osc.frequency.setValueAtTime(freq, this.ctx.currentTime);
    gain.gain.setValueAtTime(0.15, this.ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);
    osc.connect(gain);
    gain.connect(this.ctx.destination);
    osc.start();
    osc.stop(this.ctx.currentTime + duration);
  }

  playTorpedo() {
    if (this.muted || !this.ctx) return;
    const now = this.ctx.currentTime;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    osc.type = 'triangle';
    osc.frequency.setValueAtTime(450, now);
    osc.frequency.exponentialRampToValueAtTime(70, now + 0.35);
    gain.gain.setValueAtTime(0.3, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.35);
    osc.connect(gain);
    gain.connect(this.ctx.destination);
    osc.start(now);
    osc.stop(now + 0.35);
  }

  playPhaser() {
    if (this.muted || !this.ctx) return;
    const now = this.ctx.currentTime;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(800, now);
    osc.frequency.linearRampToValueAtTime(620, now + 0.5);
    gain.gain.setValueAtTime(0.2, now);
    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.5);
    osc.connect(gain);
    gain.connect(this.ctx.destination);
    osc.start(now);
    osc.stop(now + 0.5);
  }
}
```

### 7.2 Authentic LCARS CSS Component Architecture
```css
/* LCARS Container and Layout Rules */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  user-select: none;
  -webkit-user-select: none;
}

body {
  background-color: #000;
  color: #ff9900;
  font-family: var(--lcars-font);
  height: 100dvh;
  width: 100vw;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
}

/* LCARS Pill Buttons */
.lcars-btn {
  background-color: var(--lcars-orange);
  color: #000000;
  border: none;
  outline: none;
  padding: 10px 18px;
  border-radius: 20px;
  font-family: inherit;
  font-size: 14px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  touch-action: manipulation;
  transition: filter 0.1s ease;
}

.lcars-btn:active {
  filter: brightness(1.4);
}

.lcars-btn.lilac { background-color: var(--lcars-lilac); }
.lcars-btn.blue  { background-color: var(--lcars-blue); }
.lcars-btn.red   { background-color: var(--lcars-red); color: #fff; }
.lcars-btn.amber { background-color: var(--lcars-amber); }

/* LCARS Elbow Bracket (Top-Left Accent) */
.lcars-elbow {
  display: flex;
  flex-direction: column;
  width: 70px;
  flex-shrink: 0;
}

.lcars-elbow-top {
  height: 40px;
  background-color: var(--lcars-orange);
  border-bottom-left-radius: 24px;
}

.lcars-elbow-stem {
  flex-grow: 1;
  width: 32px;
  background-color: var(--lcars-lilac);
  margin-bottom: 8px;
}
```

### 7.3 Canvas Combat Arc & Vector Calculation
```javascript
// Calculate target bearing and shield quadrant hit
function calculateHitQuadrant(shipRotation, impactAngle) {
  // Normalize angles into standard 0 - 2PI range
  let relative = (impactAngle - shipRotation) % (2 * Math.PI);
  if (relative < 0) relative += 2 * Math.PI;

  const deg = (relative * 180) / Math.PI;
  if (deg >= 315 || deg < 45) return 'FORWARD';
  if (deg >= 45 && deg < 135) return 'STARBOARD';
  if (deg >= 135 && deg < 225) return 'AFT';
  return 'PORT';
}
```

---

## 8. Step-by-Step AI Agent Prompt & Execution Protocol

When invoking the AI coding model to generate the actual game file, use the following operational instructions:

### The Master Prompt to Feed the AI:
```text
You are an expert game developer, LCARS interface designer, and browser runtime specialist.

Create a complete, single-file HTML5 mobile Star Trek game titled "STAR TREK: FLEET VANGUARD" in one self-contained `index.html` file.

Strict Constraints:
1. All HTML, CSS, JavaScript, and Web Audio API must be contained in this single file. No external dependencies, CDNs, or external assets.
2. Mobile First: Target 100dvh portrait view on mobile devices, with dynamic layout adjustments if rotated to landscape. Ensure all controls are thumb-accessible (min 44x44px hitboxes).
3. Authentic LCARS design: Use the classic LCARS color scheme (amber, lilac, cyan, orange, alert red), authentic pill buttons, elbow borders, and high-contrast monospace status gauges.
4. Functional Game Mechanics:
   - Ship Subsystems: Power management matrix for Engines, Shields, Weapons, and Sensors.
   - Interactive Canvas: Tactical combat space grid featuring player starship, enemy ships (Klingons with cloaking, Romulans with heavy plasma), shields with 4 quadrants, phaser ray animations, and torpedo arcs.
   - 4 Switchable Panels: [HELM] for sector navigation and impulse steering, [TACTICAL] for combat targeting and weapon firing, [ENG] for dynamic power routing, [SENSORS] for quadrant scanner and anomaly analysis.
   - Web Audio FX: Procedural synthesized sounds for button chirps, phaser beams, torpedoes, impacts, and Red Alert klaxon.
   - Encounters & Stardate: Random mission log events (away team calls, planetary scans, distress signals) with branching tactical choices.
5. Code Quality: Clean, bug-free, modern ES6+ JavaScript. Error-proof touch event handling (`touchstart`/`pointerdown`). Include automatic high score and state saving to localStorage.

Output the complete, fully formed code ready for immediate production deployment.
```

---

## 9. Quality Assurance & Acceptance Checklist

Before delivering the game file to the client, verify every criterion below:

- [ ] **Zero-Asset Launch:** Double-click `index.html` locally in a browser without an HTTP server; the game boots and runs without CORS errors or broken asset icons.
- [ ] **Mobile Touch Isolation:** Pinch-to-zoom is disabled; swipe gestures do not trigger browser history navigation or page refresh.
- [ ] **Audio Policy Compliant:** Audio initializes on the first UI tap without throwing `Uncaught DOMException: AudioContext was not allowed to start`.
- [ ] **Subsystem Integrity:** Allocating 100% power to Engines visibly increases movement speed and evasion. Allocating to Weapons reduces torpedo reload time.
- [ ] **LCARS Compliance:** High contrast visuals, no generic rounded rects, correct LCARS typography hierarchy, and authentic color codes.
- [ ] **Combat Loop Completeness:** Hostiles take damage, their shields deplete, they drop salvage upon destruction, and the player can be destroyed (triggering a Game Over / Restart screen).
- [ ] **Viewport Resilience:** Fits iPhone Safari with bottom notch/home bar and Android Chrome with URL bar collapse without scrollbars appearing.