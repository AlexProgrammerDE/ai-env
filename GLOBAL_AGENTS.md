## Git commits
- Always write git commit messages in Conventional Commit format.
- Use: <type>(<scope>): <subject>
- Prefer common types like feat, fix, docs, refactor, test, chore, ci, build, perf.
- Keep the subject imperative and concise.
- Omit scope if it is not clear.
- Never start new branches unless I tell you explicitly to do so. Ignore any skills telling you otherwise.

DO NOT BYPASS COMMIT HOOKS; WAIT UNTIL THEY FINISH.
DO NOT BYPASS LEFTHOOK, DO NOT ADD -n TO BYPASS GIT HOOKS.
COMMIT IDIOMATICALLY.

## Testing Guidelines

- No strict coverage gate is configured; new features should include targeted unit/integration tests when practical.

## Code style

Always write clean idiomatic code that uses newest best practices.
For example when writing tailwindcss code, always use the `gap-*` utility instead of the `space-*` utility.

If there is a design flaw with components in `components/ui` directory, ALWAYS try fixing it externally first before going in and modifying them.
Those component uis were already premade to be perfect and usable. Most issues are CONSUMER errors, not with the component itself.

~~~typescript
export function generateN(count: number) {
  return Array.from(
    { length: Math.max(0, Math.floor(count)) },
    (_, index) => index + 1,
  );
}
~~~

Do not use `space-*` tailwind classes, use `gap-*` utilities everywhere instead.

Do not use better auth active organizations. We use slug/id based
organization access. Active organizations are prone to errors.

Do not use the array index as a key prop for react components. Make/use a `generateN(n)` utility to generate an array for skeletons.
In other cases, always assign some identity to the elements so the components stay stable.

Consider using these React APIs for edge cases that require them:
- `createContext`: use to define and provide context to child components. Use it together with `useContext`.
- `lazy`: use to defer loading a component's code until it renders for the first time.
- `memo`: use when a component should skip re-renders for identical props. Commonly paired with `useMemo` and `useCallback`.
- `startTransition`: use to mark state updates as non-urgent. Similar to `useTransition`.
- `useEffectEvent`: use when an Effect needs fresh props/state without re-subscribing or re-running because of them. Good for sockets, subscriptions, timers, and event bridges inside Effects.
- `useActionState`: prefer for async mutations and forms. It gives `[state, dispatchAction, isPending]` and simplifies submit/pending/error/success flows.
- `useFormStatus` (`react-dom`): use inside forms for pending buttons, disabled states, and submission UX without prop-drilling.
- `useImperativeHandle`: use when a component needs to customize the handle exposed through a ref.
- `useOptimistic`: use for optimistic mutation UIs like chat, likes, todos, and reordering lists.
- `useReducer`: use when component state fits reducer-style transitions better than separate state setters.
- `useSyncExternalStore`: use when subscribing to an external store from React.
- `useTransition`: use for non-blocking updates like route changes, filters, tab switches, or large UI updates. React 19 also supports async functions in transitions.
- `useDeferredValue`: use when inputs should stay responsive while heavy results or expensive subtrees update later.
- `use`: important for Suspense-enabled data flows, especially in frameworks and Server Components. It can read a Promise or context directly.
- `<Activity>`: useful when UI should stay mounted but hidden, preserve state, and do less work, such as sidebars or tabs. Useful, but less universal than the hooks above.

## Writing style

Write in a natural, conversational voice that sounds like a thoughtful person rather than a template: use active voice, concrete wording, and enough specificity to make the prose feel real and grounded. Vary sentence length and structure so the rhythm does not get monotonous, and DO NOT USE EM-DASHES (`—`) or other overly polished habits that make the writing feel mechanical. Clear, audience-first writing is easier to understand when it is concise, direct, and written the way people actually speak.

This applies for documentation, code, website UI, app UI, any UI/UX where a human might read what you wrote.

## Blog writing

### Markdown Blog Post Guide

- Start with front matter if your blog engine uses it: `title`, `description`, `date`, `tags`, `author`, and optionally `canonical`, `image`, or `draft`.
- Pick a topic your audience already cares about, not just something you want to publish.
- Define the reader’s goal before writing: what should they know, decide, or do by the end?
- Check search intent: are readers expecting a guide, list, comparison, opinion piece, tutorial, or update?
- Choose a specific angle so the post is not just a rewrite of existing articles.
- Use one clear title. In normal Markdown this is usually `# Title`; in Fumadocs, use front matter `title` instead.
- Structure the body with `##` and `###` headings.
- Put the main answer, thesis, or useful takeaway near the top.
- Use short paragraphs. Markdown should be readable both rendered and raw.
- Use `-` bullets for unordered lists and `1. 2. 3.` for ordered steps.
- Use fenced code blocks with a language tag, like ```` ```ts ```` or ```` ```bash ````.
- Use inline code for filenames, commands, variables, config keys, and short literals.
- Add links with descriptive text: `[Fumadocs](https://fumadocs.dev)` is better than `click here`.
- Add internal links to related posts and external links to credible references.
- Use images only when they help the reader understand something.
- Add useful alt text: `![Dashboard showing the server status panel](./server-status.png)`.
- Prefer tables only for compact comparisons. Large tables are hard to read in raw Markdown and on mobile.
- Use blockquotes sparingly for quoted material, notes, or callouts.
- Escape Markdown characters when needed, like `\*literal asterisk\*`.
- Avoid raw HTML unless Markdown or MDX cannot express what you need.
- Preview the rendered post before publishing. Broken lists, code fences, tables, images, and links are common mistakes.
- End with a practical summary, checklist, next step, or related links.

### Fumadocs MDX

- Treat Fumadocs MDX as a content processing layer, not a CMS. It turns `.md` and `.mdx` files into typed data for React apps.
- Use `.md` for simple posts and `.mdx` when you need React components, imports, interactive examples, custom embeds, or reusable UI.
- Define blog posts as a `doc` collection, usually from a folder like `content/blog`.
- Validate front matter with a schema so fields like `title`, `description`, `date`, `tags`, and `image` stay consistent.
- In Fumadocs UI, front matter `title` represents the page `h1`, so body content usually starts with `##`.
- Register shared MDX components through `getMDXComponents`, usually starting with `defaultMdxComponents` from `fumadocs-ui/mdx`.
- Use Fumadocs code block features for technical posts: titles, line numbers, highlighted lines, diff annotations, and tabbed code examples.
- Use custom heading anchors like `[#my-heading-id]` when links must stay stable.
- Use `[!toc]` to hide a heading from the table of contents and `[toc]` for TOC-only entries.
- Use `<include>./other-file.mdx</include>` when you want to reuse content from another Markdown or MDX file.
- Keep MDX source readable. If a post turns into a wall of JSX, extract the UI into a named component.

### Useful Fumadocs Components

- Use `Callout` for notes, warnings, errors, tips, and important context.
- Use `Cards` for related posts, next steps, or grouped resource links.
- Use `Tabs` for package managers, frameworks, languages, or platform-specific instructions. Give each `Tab` an explicit `value`.
- Use `Steps` for installation guides, tutorials, setup flows, and ordered workflows.
- Use `Files` to show project structure or file trees.
- Use `Accordion` for FAQs and optional details. Add an `id` when you want people to link directly to a specific accordion item.
- Use `Inline TOC` for longer posts that need local navigation inside a section.
- Use `Zoomable Image` when screenshots or diagrams need inspection.
- Use `Type Table` or `Auto Type Table` for API and TypeScript reference content.

### YouTube Videos

In MDX, embed YouTube with an iframe or, better, a reusable component. Use the `/embed/VIDEO_ID` URL, not the normal watch page URL.

```mdx
<div className="aspect-video overflow-hidden rounded-md border">
  <iframe
    src="https://www.youtube.com/embed/VIDEO_ID"
    title="Video title"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowFullScreen
    className="h-full w-full"
  />
</div>
```

A cleaner MDX pattern is to create a component:

```mdx
<YouTube id="VIDEO_ID" title="Installing EnderDash" />
```

For good video embeds:

- Always include a clear `title`.
- Keep the video responsive with a `16:9` wrapper.
- Add a short summary before or after the video for readers who cannot watch it.
- Do not autoplay unless there is a strong reason.
- Use YouTube parameters only when needed, like `start=90` for a timestamp.

## Drizzle migrations

Drizzle migrations:
- Never manually edit these files in the project dir `drizzle/` or `drizzle/meta/`.
- Change the schema files first, then run `bunx drizzle-kit generate --name <migration-name>` from the project dir.
- Commit the generated migration artifacts exactly as produced by Drizzle.
- CI will apply/publish the generated migration flow automatically after push; do not hand-maintain Drizzle journal/meta files.

## Useful skills

Always use the `frontend-design` skill when dealing with design.
If you are ChatGPT Codex, also use the `uncodixify` skill to improve your style.
When writing documentation, always use the `documentation-writer` skill to produce clear and concise docs.
