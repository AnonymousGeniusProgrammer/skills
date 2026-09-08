---
name: wpf-visual-consistency
description: Refactor or implement WPF .NET Framework + MVVM presentation-layer components using the repository visual-consistency standard. Use when changing Views, ViewModels, styles, templates, resources, themes, or semantic-state-to-appearance mappings, especially when presentation choices have leaked into ViewModels or visual definitions are duplicated, local, hard-coded, missing, or inconsistently reused.
---

# WPF Visual Consistency

Apply the repository visual-design engineering guideline through the following
refactoring process.

The engineering guideline is the authority for architectural rules.
The active work item is the authority for scope, required outcomes, and
change-specific sequencing.

## Process

### 1. Start at the ViewModel presentation boundary

Inspect the bindable surface used by the affected View before reorganizing
styles or resources.

Look specifically for properties that directly encode appearance, including:

- icon types such as `IconChar`;
- visual-tone or color-category enums;
- `Color`, `Brush`, font, or style values;
- presentation-driven `Visibility`;
- fixed canvas coordinates, offsets, or other presentation geometry.

Classify each candidate before changing it:

- semantic/application state;
- presentation leakage;
- user-facing content;
- genuine runtime spatial/layout data.

Do not replace a presentation property with another generic presentation
abstraction such as `VisualTone`.

For presentation leakage, identify the business or feature meaning that caused
the visual choice and expose that meaning instead.

### 2. Migrate semantic state and its visual mapping together

Handle one complete state dimension at a time.

Before modifying it, record the existing mapping:

    current semantic condition
        -> exposed ViewModel value
        -> resulting icon/color/style/layout

Replace the appearance-facing ViewModel value with the narrowest meaningful
semantic state.

Immediately establish the corresponding mapping in the presentation layer:

    semantic state
        -> XAML mapping
        -> visual state

Prefer a `Style`, `DataTemplate`, template trigger, or data trigger when the
mapping is naturally declarative. Use a focused converter or selector when the
mapping would otherwise become difficult to understand.

Preserve every reachable existing state unless the work item explicitly
requests a redesign.

Remove the old appearance-facing property only after its replacement and all
consumers are verified.

### 3. Then inspect the View for visual-system inconsistencies

After the presentation boundary for the slice is clear, inspect the affected
XAML.

Look for:

- literal colors or brushes;
- repeated local styles or templates;
- shared styles recreated locally;
- incorrect or misleading resource keys;
- missing styles where equivalent controls should share presentation;
- theme-sensitive values using inappropriate resource lookup;
- feature dictionaries that redefine application-level theme resources.

Do not immediately centralize everything found.

Treat each style, template, or visual value as a candidate for the next step.

### 4. Decide reuse before creating or moving resources

For every candidate, compare it with the existing visual infrastructure.

Use this decision order:

1. **Existing resource expresses the same meaning**
   -> reuse it.

2. **Existing resource represents the same concept but is incomplete**
   -> consider improving it, but first inspect its existing consumers and
   ensure the change remains correct for them.

3. **No existing resource matches and multiple consumers demonstrate the
   same presentation concept**
   -> create or generalize a shared resource.

4. **The presentation is genuinely specific to one View or feature**
   -> keep it at that narrower scope.

Do not generalize because two values currently have the same color, dimensions,
or appearance. Generalize only when their presentation meaning is shared.

Before creating a new semantic resource, search the existing dictionaries for
synonyms and legacy equivalents.

### 5. Establish a clear presentation-resource chain

For migrated presentation, aim for an understandable dependency chain:

    semantic ViewModel state
        -> View mapping
        -> Style / Template
        -> semantic visual resource
        -> active theme value

When the chain is unclear, trace the actual resource lookup before modifying
it.

If a required resource is missing, introduce it at the ownership scope defined
by the engineering guideline.

If an existing resource is wrongly scoped or named, migrate its consumers
before removing or replacing it.

Avoid leaving both old and new presentation contracts active longer than
necessary.

### 6. Hand off for review

After completing the requested implementation, hand the resulting change to
the repository's review workflow.

Provide the review process with the authorities it needs to evaluate the
change:

- the active work-item/task specification that defines work-item compliance;
- the identity and location of the applicable visual-design engineering
  guideline that defines engineering compliance;

## Key working principle

Do not begin visual-consistency refactoring by designing a complete resource system.

Work outward from actual presentation behavior:

    ViewModel leakage
        -> semantic state
        -> View mapping
        -> concrete style/resource need
        -> reuse/generalization decision
        -> shared visual infrastructure

This keeps the visual system derived from demonstrated application needs instead of speculative abstractions.
