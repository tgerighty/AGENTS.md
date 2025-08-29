## styling

- always use tailwind for styling, prefer using simple styles using flex and gap. Margins should be avoided, instead use flexbox gaps, grid gaps, or separate spacing divs.

- Use shadcn theme colors instead of tailwind default colors. This way there is no need to add `dark:` variants most of the time.

- `flex flex-col gap-3` is preferred over `space-y-3`. same for the x direction.

- Try to keep styles as simple as possible, for breakpoint too.

- to join many classes together use the `cn('class-1', 'class-2')` utility instead of `${}` or other methods. this utility is usually used in shadcn compatible projects and mine is exported from `website/src/lib/cn` usually. Prefer doing `cn(bool && 'class')` instead of `cn(bool ? 'class' : '')`

- prefer `size-4` over `w-4 h-4`

## components

This project uses shadcn components placed in the website/src/components/ui folder. never add a new shadcn component yourself writing code, instead use the shadcn cli installed locally instead.

Try to reuse these available components when you can, for example for buttons, tooltips, scroll areas, etc.