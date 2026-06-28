# Acme SaaS — component library (notes)

Our UI components live in `@/components/ui`. Built on shadcn, lightly customized.
These are the ones people most often get wrong.

## Button — `@/components/ui/button`
The component for any clickable **action** (submit, save, delete, open a dialog).
Not for navigation — links use `<TextLink>`.

- Variants: `primary` (default), `secondary`, `ghost`, `destructive`.
- Sizes: `sm`, `md` (default), `lg`.
- For destructive actions (delete, remove) use `variant="destructive"`.
- Icon-only buttons must pass `aria-label`.

People keep hand-rolling `<button className="bg-indigo-600 ...">`. Don't. Use this.

## TextLink — `@/components/ui/text-link`
For **navigation** (going somewhere). Renders an `<a>` with our underline/hover
treatment. Don't style a raw `<a>` to look like this; don't use a Button for nav.

## Card — `@/components/ui/card`
Surface container for a grouped block of content. Use `<Card>`, `<CardHeader>`,
`<CardTitle>`, `<CardContent>`.

- Do NOT nest a Card inside a Card. If you feel the urge, the inner thing should
  be a plain section with spacing, not another surface.

## Input / Field — `@/components/ui/input`, `@/components/ui/field`
Form inputs. Always wrap an Input in a `<Field>` so it gets a label and error
slot. A bare `<input>` with no label is not acceptable.

## Modal — `@/components/ui/modal`
Our dialog component. The old `LegacyModal` is deprecated — do not use it in new
code; migrate when you touch it.
