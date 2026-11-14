# Design system and UI/UX

Consult this whenever you create or change anything visible. The design system is the single source of truth for how the product looks and behaves. Features consume it; they never redefine it.

## Layers

1. **Design tokens.** These are the primitive values: color palette, spacing scale (4- or 8-pt), type scale, radii, shadows or elevation, motion durations and easings, z-index scale and breakpoints. They are stored once (CSS custom properties, a Tailwind theme, or a TS tokens file) and exported to code.
2. **Semantic tokens.** Each one maps intent to a primitive, for example `color.bg.surface`, `color.text.muted`, `color.border.danger` or `space.inset.md`. Components use **only semantic tokens**. Themes such as light, dark and brand variants are implemented by remapping semantic tokens.
3. **Primitives.** `Button`, `Input`, `Select`, `Checkbox`, `Dialog`, `Toast`, `Tooltip`, `Card`, `Stack`/`Grid`, `Text`/`Heading`, `Icon`, `Skeleton`, `Spinner`. Build them on headless, accessible foundations (Radix, React Aria, Angular CDK, Headless UI, Ark) rather than hand-rolled focus and ARIA logic.
4. **Patterns.** Composed parts with UX rules built in: `FormField` (label, hint, error), `DataTable`, `EmptyState`, `ErrorState`, `ConfirmDialog`, `Pagination`, `PageHeader`.
5. **Feature components and pages.** These consume layers 1–4 and hold product logic. They are not reused across features until they have been promoted into layer 4.

## Component API rules

- **Props are typed, minimal, and named after intent.** Use `variant: 'primary' | 'secondary' | 'danger'` and `size: 'sm' | 'md' | 'lg'`, not `isRed` or `bigPadding`.
- **Variants come from a variant map,** for example `cva` or a typed lookup object, rather than ad-hoc conditionals.
- **Prefer composition** (children, slots, compound components such as `<Card><Card.Header/></Card>`) over prop explosion.
- **Accept native attributes.** Forward refs and spread the rest of the props onto the root element so consumers keep native behaviour.
- **Support both controlled and uncontrolled use** for inputs.
- **Primitives take no data fetching and no business logic.**
- **Every primitive has a story** (Storybook or Ladle) showing all its variants and states, and is documented with do's and don'ts.

## UX behaviours (non-negotiable)

- **Feedback.**
  - Every action acknowledges itself within 100 ms: a pressed state, a spinner, or an optimistic update.
  - Operations that take more than 1 s show progress.
  - Destructive actions offer confirmation or undo.
- **States.** Design the loading state with skeletons that match the final layout, so content doesn't shift. Also design the empty state (explain it, then give the next action), the error state (say what happened in plain words and give a retry or recovery), and the success state.
- **Forms.**
  - Labels are always visible, never placeholder-only.
  - Validate on blur and on submit.
  - Show inline errors beside the field, and focus the first invalid field on submit.
  - Disable submit while it is in flight.
  - Preserve the user's input when an error occurs.
- **Navigation.**
  - The current location is always clear.
  - The back button works, because state lives in the URL where it should: filters, tabs, pagination.
  - Deep links work.
- **Consistency.** One pattern per problem across the product, and the same words for the same concepts. The copy is short, specific and action-oriented.
- **Hierarchy.** One primary action per view. Use spacing and type weight to group and rank content before reaching for color or borders.
- **Motion.** Motion is purposeful, short (150–300 ms), never blocks input, and is disabled under `prefers-reduced-motion`.
- **Accessibility.** WCAG 2.2 AA, as in the SKILL quality gate. Never convey meaning by color alone.
- **Internationalization.** Every string lives in message catalogs. Layouts tolerate text that is 30% longer, and numbers, dates and currency go through `Intl`.

## Visual simplicity

Beautiful and simple comes from restraint:

- a limited palette: neutrals plus one accent plus status colors;
- 2–3 type sizes per screen;
- generous whitespace on the spacing scale;
- aligned grids;
- consistent radii and elevation;
- one icon family.
