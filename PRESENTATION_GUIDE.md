# Fridge2Fork Presentation Guide

## 1. What This Project Is

Fridge2Fork is a phone-first AI meal decision assistant for college students and young professionals.

The product starts with ingredients a person already has instead of asking them to search for a recipe first. It combines:

- Available ingredients
- Available cooking time
- Nutrition goals
- Additional spending
- Food-waste priority

The product promise is:

> Snap what you have. Set your constraints. Get 3 meals you can actually make right now.

This repository contains a polished interactive prototype. It is designed to demonstrate the complete user experience, not to provide a production backend or real AI system.

## 2. Quick Start

Requirements:

- Node.js compatible with the installed Next.js version
- npm

Install dependencies if needed:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

Open:

```text
http://localhost:3000
```

Useful commands:

```bash
npm run build   # Production compilation check
npm run lint    # ESLint check
npm run start   # Run the production build after npm run build
```

For the best presentation experience:

1. Open the app at `http://localhost:3000`.
2. Use a phone-sized browser viewport to demonstrate the original product context.
3. Resize to a desktop viewport to show the responsive layout.
4. Keep the browser zoom at 100%.
5. Have the demo ingredients and constraints ready to select.

## 3. Technology Stack

- **Next.js 16** with the App Router
- **React 19**
- **TypeScript**
- **Tailwind CSS 4** is installed through the Next.js setup, although most of the prototype styling is intentionally written in `app/globals.css` for fast visual control.
- **Lucide React** for interface icons
- **Unsplash image URLs** for the food and refrigerator imagery
- **No backend, database, authentication, or external AI API**

## 4. Project Structure

```text
app/
  layout.tsx       Global document metadata and root layout
  page.tsx         Complete interactive prototype and mock data
  globals.css      Visual system and responsive layout rules

package.json       Scripts and dependencies
next.config.ts     Next.js configuration
README.md          Default Next.js starter documentation
PRESENTATION_GUIDE.md  This presentation and architecture guide
```

### Why the prototype is currently in one page

The prototype is intentionally kept small. Each major screen is represented by a React component inside `app/page.tsx`, while the top-level `Home` component controls which screen is displayed.

This makes the demo easy to follow and keeps the prototype deterministic. A production version should split these components into separate files and routes once the product behavior is validated.

## 5. Application Architecture

The app is a client-side state machine.

The main screen state is declared in the top-level component:

```ts
type Screen =
  | "home"
  | "scan"
  | "manual"
  | "constraints"
  | "recommendations"
  | "meal"
  | "cooking"
  | "saved";
```

The current screen is stored with React state:

```ts
const [screen, setScreen] = useState<Screen>("home");
```

Navigation is handled by passing `setScreen` down as a `go` callback. For example:

```tsx
<button onClick={() => go("scan")}>Scan my fridge</button>
```

The top-level component renders the matching screen:

```tsx
if (screen === "home") return <HomeScreen go={setScreen} />;
if (screen === "scan") return <ScanScreen go={setScreen} />;
```

There is no URL routing between the prototype screens. This is deliberate: the demo behaves like a single mobile app and avoids backend or route complexity.

## 6. Main User Flow

The intended presentation flow is:

```text
Home
  -> Scan your fridge
  -> Confirm ingredients
  -> Set constraints
  -> Loading state
  -> 3 ranked meals
  -> Why this ranking? (optional)
  -> Egg & Spinach Toast details
  -> Cooking mode
  -> Complete four cooking steps
  -> You're done
```

The manual ingredient path is an alternate entry point:

```text
Home
  -> Enter ingredients manually
  -> Add/remove ingredient chips
  -> Continue
  -> Set constraints
```

## 7. Screen-by-Screen Explanation

### 7.1 Home

Component: `HomeScreen`

Purpose:

- Establish the product promise quickly.
- Give the user two clear ways to begin.
- Explain the three product benefits.

Important UI:

- Fridge2Fork wordmark
- "What can you cook right now?"
- Scan My Fridge primary action
- Enter Ingredients Manually secondary action
- Three value cards:
  - Use what you have
  - Hit your goals
  - Decide in 2 minutes
- Bottom navigation for Home, My ingredients, and Saved

Presentation talking point:

> Most recipe apps begin with a dish search. Fridge2Fork begins with the user's real situation: what is already in the fridge and what constraints exist tonight.

### 7.2 Scan Your Fridge

Component: `ScanScreen`

Purpose:

- Simulate computer vision without implementing real computer vision.
- Make the product feel immediate and practical.

Behavior:

- A refrigerator image is displayed.
- Detection labels appear over the image.
- A scanning line animates for approximately 1.2 seconds.
- The screen presents five detected ingredients:
  - Eggs, quantity 4
  - Spinach
  - Tomato, quantity 2
  - Bread, quantity 4 slices
  - Onion, quantity 1

The `Looks right` button always proceeds to constraints because the goal is a reliable demo path.

Presentation talking point:

> This is a simulated computer-vision step in the prototype. In a production build, this surface would connect to a camera flow and an ingredient recognition service.

### 7.3 Manual Ingredients

Component: `ManualScreen`

Purpose:

- Provide a fallback for users who do not want to scan.
- Demonstrate that the product is useful even without computer vision.

Behavior:

- The user can type an ingredient.
- Pressing `Add` or Enter creates a chip.
- Each chip has an X control for removal.
- `Continue` leads to the same constraints screen as scanning.

### 7.4 Constraints

Component: `ConstraintsScreen`

Purpose:

- Translate a vague cooking need into concrete decision criteria.

Selection groups:

- **Time available:** 10 min, 15 min, 30+ min
- **Nutrition goal:** High Protein, Low Calorie, Balanced, Anything
- **Extra spending:** ₹0, ≤ ₹50, Doesn’t matter
- **Cooking effort:** Lazy, Normal, I want to cook

The demo defaults are:

- 15 min
- High Protein
- ₹0
- Normal

When `Find my meals` is pressed, the prototype shows a short loading state:

1. Checking your ingredients...
2. Matching your goals...
3. Ranking your best options...

After approximately 1.7 seconds, it opens the recommendations screen.

Presentation talking point:

> The loading sequence communicates that the product is making a decision from several inputs. It is not a generic chatbot response; it is a constrained ranking workflow.

### 7.5 Recommendations

Component: `RecommendationsScreen`

This is the most important product screen.

The summary states:

```text
5 ingredients · 15 min · High protein · ₹0 extra
```

The three deterministic meals are:

| Rank | Meal | Time | Calories | Protein | Fridge Fit |
| --- | --- | ---: | ---: | ---: | ---: |
| 1 | Egg & Spinach Toast | 12 min | 430 kcal | 29g | 100% |
| 2 | Masala Egg Sandwich | 10 min | 470 kcal | 27g | 92% |
| 3 | Spinach Egg Bhurji | 14 min | 390 kcal | 31g | 86% |

Each recommendation includes:

- Food image
- Rank or Best Match badge
- Time, calorie, and protein stats
- Additional cost indicator
- Fridge Fit progress bar
- Description
- Recipe action

The first card also shows the reasons it ranked first:

- Fits the 15-minute limit
- High protein with no extra spending
- Uses spinach first

#### Food-waste feature

The recommendation screen includes the supporting callout:

> Use this first. Your spinach is the most perishable ingredient, so we prioritized meals that use it.

This makes food-waste reduction part of the recommendation logic without turning it into the main experience.

#### Why this ranking?

The button opens a bottom sheet with five scoring dimensions:

- Ingredient Fit — 100%
- Nutrition Fit — 94%
- Time Fit — 100%
- Cost Fit — 100%
- Waste Priority — 88%

Presentation talking point:

> Fridge2Fork earns trust by showing why a meal ranked well. The recommendation is explainable instead of feeling like a random AI result.

### 7.6 Meal Details

Component: `MealDetails`

The details page uses the selected meal from the recommendation list.

It shows:

- Hero food image
- Fridge Fit badge
- Time, calories, protein, and cost
- Ingredient list
- Numbered preparation steps
- Start cooking action

For the main demo meal, the ingredients are:

- 2 eggs
- 2 slices bread
- 1 handful spinach
- 1 tomato
- 1/4 onion
- Salt & pepper

The exact steps depend on the selected mock meal, but all three meals have four steps.

### 7.7 Cooking Mode

Component: `CookingScreen`

Cooking mode intentionally feels different from the rest of the app:

- Dark forest-green background
- Lime progress indicators
- Large step image
- Step counter
- One prominent `Done` action

The user progresses through four steps. The screen tracks the current step locally with React state:

```ts
const [step, setStep] = useState(0);
```

After the final step, the completion state shows:

- You’re done!
- Calories
- Protein
- Cook something else
- Done

The displayed `12:00` timer is currently a visual timer treatment. It does not count down.

### 7.8 Saved Placeholder

The Saved bottom navigation item opens a simple placeholder:

> Your saved meals will appear here.

It is intentionally out of scope for this prototype. The heart icons on meal cards are visual affordances and are not connected to persistence.

## 8. Mock Data and Recommendation Logic

Mock meal data is declared near the top of `app/page.tsx` in the `mealData` array.

Each meal contains:

```ts
{
  name,
  time,
  calories,
  protein,
  fit,
  image,
  description,
  steps
}
```

The current recommendation list is deterministic. The selected constraint values are interactive for the presentation, but they do not dynamically recalculate the rankings.

This is appropriate for the prototype because the requested demo scenario is fixed:

```text
Ingredients: eggs, spinach, tomatoes, bread, onion
Time: 15 minutes
Nutrition: high protein
Extra spending: ₹0
Result: Egg & Spinach Toast ranked first
```

A production recommendation engine would replace this hardcoded array with a scoring pipeline. A simple future scoring model could combine:

```text
Total score =
  ingredient fit
  + nutrition fit
  + time fit
  + cost fit
  + waste priority
```

Each input would be normalized, weighted according to the user's selected constraints, and used to sort candidate meals.

## 9. Responsive Design

The visual system is defined in `app/globals.css`.

### Mobile

Below 700px:

- The app uses a phone-first single-column layout.
- Bottom navigation spans the viewport.
- Cards stack vertically.
- CTAs are full width.
- The recommendation experience is optimized for quick scanning while standing near a fridge.

### Tablet

From 700px to 960px:

- The app expands to an 860px canvas.
- Benefits become a multi-column row.
- Recommendations use a two-column layout.
- The featured recommendation spans the full row.

### Desktop

Above 960px:

- The app expands to a 1,180px content canvas.
- The Home page has wider typography and spacious media.
- Benefits appear in three columns.
- Recommendations use three columns.
- Scan and meal images become much larger.
- Meal details use wider content while keeping readable text widths.
- Cooking Mode becomes a centered workspace instead of a narrow phone simulation.

The core colors are stored as CSS variables:

- `--cream`: warm off-white background
- `--paper`: card surface
- `--deep`: forest-green primary color
- `--green`: secondary green accent
- `--lime`: active and success accent
- `--muted`: supporting text
- `--line`: subtle borders

## 10. Animation and Interaction Design

The prototype uses restrained motion to communicate system state:

- Scan line moves across the refrigerator image.
- Loading orbit pulses while meals are being ranked.
- Recommendation cards fade and rise into view with a short stagger.
- Fridge Fit bars animate from zero to their score.
- The ranking explanation sheet slides up from the bottom.
- Buttons slightly lift on hover.

The goal is to make the app feel responsive without distracting from the decision flow.

## 11. Suggested Presentation Script

### Opening

> Fridge2Fork is an AI meal decision engine for the moment when you have ingredients, but no idea what to cook. Instead of making users search for a dish, it starts with what they already have and ranks meals around their real constraints.

### Home

> The first screen makes the promise clear: scan what is available, set a few constraints, and get three meals that are realistic right now.

### Scan

> I’ll scan the fridge. This prototype simulates the computer-vision step and identifies five ingredients: eggs, spinach, tomatoes, bread, and onion.

### Constraints

> Now I can define the decision: 15 minutes, high protein, and no additional spending. This is important because the system is not just generating recipes. It is solving a constrained meal decision.

### Recommendations

> The result is a ranked list, not an endless feed. Egg & Spinach Toast is the best match because it is 100% Fridge Fit, fits the time limit, meets the protein goal, costs nothing extra, and uses spinach first.

### Explainability

> The `Why this ranking?` panel makes the ranking understandable. It shows ingredient, nutrition, time, cost, and waste-priority scores.

### Cooking

> Once I choose a meal, the product switches into Cooking Mode. The interface becomes more focused and walks me through one step at a time.

### Closing

> The prototype demonstrates the complete decision journey from uncertainty to a meal that can actually be cooked now. The next production steps would be real camera recognition, a meal database, personalization, persistence, and an actual ranking service.

## 12. Acceptance Test

Use this checklist before presenting:

- [ ] Home screen loads at `http://localhost:3000`.
- [ ] `Scan my fridge` opens the scan screen.
- [ ] Scan animation appears briefly.
- [ ] Five ingredients are visible.
- [ ] `Looks right` opens constraints.
- [ ] Defaults show 15 min, High Protein, ₹0, and Normal.
- [ ] `Find my meals` shows the loading state.
- [ ] Three recommendations appear.
- [ ] Egg & Spinach Toast is ranked first.
- [ ] Fridge Fit bars and ranking reasons are visible.
- [ ] `Why this ranking?` opens the scoring sheet.
- [ ] `Cook this` opens meal details.
- [ ] Ingredients and numbered steps are visible.
- [ ] `Start cooking` opens Cooking Mode.
- [ ] Four cooking steps advance with `Done`.
- [ ] Completion state appears after the fourth step.
- [ ] Home, My ingredients, and Saved navigation buttons respond.
- [ ] Manual ingredient entry can add and remove chips.
- [ ] Desktop layout expands instead of remaining phone-width.

## 13. Prototype Boundaries

The following are intentionally mocked or simplified:

- No authentication
- No user accounts
- No database
- No real AI API
- No real computer vision
- No camera permission flow
- No recipe scraping
- No grocery delivery
- No real-time functionality
- No saved-meal persistence
- No countdown timer
- Constraint changes do not recalculate the hardcoded rankings
- Images are loaded from remote Unsplash URLs

These boundaries should be described as deliberate prototype decisions, not missing production features.

## 14. Production Evolution

A realistic next version could evolve in stages:

### Stage 1: Product validation

- Add analytics for scan completion, constraint selection, and meal selection.
- Test which ranking explanations users understand.
- Add actual save and favorite behavior with local storage.
- Add a working cooking timer.

### Stage 2: Data and recommendation system

- Create a meal and ingredient database.
- Normalize ingredient quantities and substitutions.
- Implement the weighted recommendation score.
- Add user-defined dietary restrictions and allergies.
- Add better food-waste estimates based on perishability and quantity.

### Stage 3: Real scanning and personalization

- Add camera capture.
- Connect image recognition to ingredient extraction.
- Ask the user to confirm uncertain detections.
- Store pantry history and preferences.
- Personalize nutrition targets and meal ranking.

### Stage 4: Production application

- Split screens into route-level files and reusable components.
- Add authentication and persistent storage.
- Add API validation and error states.
- Optimize images with `next/image` or a media CDN.
- Add automated end-to-end tests for the core flow.

## 15. Important Files to Explain During Review

- [app/page.tsx](app/page.tsx): all screen components, mock meals, state transitions, and interactive behavior.
- [app/globals.css](app/globals.css): colors, typography, cards, buttons, animations, and responsive breakpoints.
- [package.json](package.json): scripts and dependency versions.
- [app/layout.tsx](app/layout.tsx): root HTML layout and metadata.

The key idea to remember is that this is a deterministic, presentation-ready simulation of the Fridge2Fork product experience. Its value is in showing a believable end-to-end workflow and recommendation model, while keeping production infrastructure intentionally out of scope.
