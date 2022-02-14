# Storybook

## Story rendering util

<!-- prettier-ignore-start -->
```tsx
/* eslint-disable jest/no-export */
import { render, waitFor, screen } from "@testing-library/react";
import { SomeProvider } from "components/SomeProvider";
import { Story } from "@storybook/react";

// include all of your providers here
const Providers: React.FC = ({ children }) => (
  <SomeProvider>{children}</SomeProvider>
);

// eslint-disable-next-line @typescript-eslint/no-explicit-any
const customRender: typeof render = ((ui: any, options: any) =>
  render(ui, { wrapper: Providers, ...options })) as unknown as typeof render;

export * from "@testing-library/react";

export { customRender as render };

export const renderStory = <TArgs,>(
  Story: Story<TArgs>,
  props?: Partial<TArgs>
) => customRender(<Story {...(Story.args as TArgs)} {...props} />);
```

<!-- prettier-ignore-end -->
