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

## Testing style

When you make a regression test, write the regression test and test that it fails BEFORE fixing the issue.
If you don't do that, the regression test might not actually make sure the regression itself is fixed and the test would be useless.
Only make regression tests when it is reasonable. Here a list:
- Okay when you fix a bug in a technical function that handles data/computes values/handles actions
- Not required when checking if contents of files contain a string
- Not required when it's about rendering frontend. e.g. oneoff small functions that don't handle user data and are only for displaying visuals.

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

## Useful skills

Always use the `frontend-design` skill when dealing with design.
If you are ChatGPT Codex, also use the `uncodixify` skill to improve your style.
When writing documentation, always use the `documentation-writer` skill to produce clear and concise docs.
