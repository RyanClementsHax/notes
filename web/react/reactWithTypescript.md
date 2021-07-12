# React with Typescript

## Functional components
- you need to use `React.FC` to type children in functional components
- you can type the props directly without that type helper if you don't have any children to consider

## `useRef`
- to get a read only ref, use the `!` operator: `const ref = useRef<HTMLInputElement>(null!);`