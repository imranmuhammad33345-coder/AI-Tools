# Design Guidelines: AI Tools Platform

## Design Approach
**Selected System:** Material Design 3 with Linear-inspired minimalism
**Rationale:** Productivity-focused interface requiring clarity, scalability, and efficient information display across multiple tool types.

## Core Design Principles
- **Clarity over decoration:** Every element serves a functional purpose
- **Scannable hierarchy:** Users should immediately understand tool categories and actions
- **Consistent patterns:** Repeated interactions across all tools (input → process → output)
- **Progressive disclosure:** Advanced features revealed contextually

---

## Typography System

**Font Stack:** Inter (primary), JetBrains Mono (code)

**Hierarchy:**
- Page titles: text-4xl, font-bold (36px)
- Section headers: text-2xl, font-semibold (24px)
- Tool cards: text-lg, font-medium (18px)
- Body text: text-base, font-normal (16px)
- Helper text: text-sm, font-normal (14px)
- Code blocks: text-sm, font-mono (14px)

---

## Layout System

**Spacing Primitives:** Tailwind units of 2, 4, 6, 8, 12, 16
- Micro spacing (within components): p-2, gap-4
- Component spacing: p-6, p-8
- Section spacing: py-12, py-16
- Page margins: px-4 (mobile), px-8 (desktop)

**Grid Structure:**
- Container: max-w-7xl mx-auto
- Tool cards grid: grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6
- Main workspace: Two-column split (lg:grid-cols-2) for input/output tools

---

## Component Library

### Navigation
- **Top bar:** Fixed header with logo, main navigation, user account
- **Sidebar (desktop):** Collapsible tool category navigation with icons
- **Mobile:** Hamburger menu revealing full navigation drawer

### Tool Cards (Homepage)
- Medium-height cards (h-48) with icon, title, description
- Hover state: subtle elevation increase
- Click area: entire card
- Structure: Icon (top) → Title → Description → Arrow indicator

### Input/Output Panels
- **Input area:** Bordered textarea with min-h-64, rounded corners
- **Action buttons:** Prominent primary button (full width on mobile)
- **Output area:** Same styling as input with copy/download controls
- **Settings:** Collapsible panel for advanced options

### Chat Interface
- **Message bubbles:** User (right-aligned), AI (left-aligned)
- **Width:** max-w-3xl centered conversation thread
- **Input:** Fixed bottom bar with textarea + send button
- **History:** Scrollable conversation with timestamps

### Code Display
- Syntax-highlighted blocks with copy button
- Line numbers for longer snippets
- Language indicator badge

### Forms & Controls
- Input fields: border, rounded-lg, p-4, focus ring
- Dropdowns: Custom styled selects matching inputs
- Sliders: Material Design range inputs for parameters
- Toggles: Switch components for binary options

### Status & Feedback
- Loading states: Skeleton loaders matching content structure
- Error messages: Inline beneath relevant field
- Success confirmations: Toast notifications (top-right)
- Progress indicators: Linear progress bar for processing

---

## Page Layouts

### Homepage/Dashboard
- **Hero section:** 50vh height with gradient background
  - Centered headline + subheadline + primary CTA
  - No hero image (focus on tool access)
- **Tool categories:** 3-column grid of feature cards
- **Recent activity:** List of last 5 interactions
- **Footer:** Links, social, newsletter signup

### Tool Pages
- **Header:** Tool name + brief description + action button
- **Workspace:** 
  - Mobile: Stacked input → output
  - Desktop: Side-by-side panels (50/50 split)
- **Settings panel:** Right sidebar (collapsible)
- **Examples:** Expandable section below workspace

### Chat Interface
- Full-height layout (100vh minus header)
- Conversation fills available space
- Input fixed at bottom with auto-expanding textarea

---

## Images

**Hero Section:** Abstract AI/technology illustration (geometric shapes, neural network patterns) - vibrant but professional
**Tool Category Icons:** Use Heroicons for consistency (code, chat, language, image, document, edit icons)
**Empty States:** Friendly illustrations prompting first action

**Image Placement:**
- Homepage hero: Full-width background illustration with overlay
- Tool cards: Icon-only (no images)
- Empty states: Centered illustrations in workspace areas

---

## Responsive Behavior

**Breakpoints:**
- Mobile: < 768px (single column, stacked layouts)
- Tablet: 768px-1024px (2-column grids)
- Desktop: > 1024px (full 3-column grids, sidebars visible)

**Mobile Optimizations:**
- Bottom navigation bar for primary tools
- Full-width input/output areas
- Collapsible settings into bottom sheet
- Swipe gestures for tool switching

---

## Accessibility Standards
- Minimum touch targets: 44×44px
- Focus indicators: 2px ring on all interactive elements
- ARIA labels on icon-only buttons
- Keyboard shortcuts for power users (displayed on hover)
- High contrast text ratios maintained throughout

---

**Quality Mandate:** This platform must feel cohesive, professional, and efficient. Every tool should follow identical interaction patterns while maintaining visual interest through thoughtful spacing and typography hierarchy.