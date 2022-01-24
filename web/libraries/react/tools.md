# Tools

## Modifying children with typescript

```ts
// this example sets every other prop in side the Properties component as having a light background
import { Children, cloneElement } from "react";
import c from "classnames";

export interface PropProps extends React.HTMLAttributes<HTMLTableRowElement> {
  name: string;
  value: string;
}

const Prop: React.FC<PropProps> = ({ name, value, ...props }) => (
  <tr {...props} key={name} className={c(s.propRow, props.className)}>
    <td className="text-muted text-capitalize p-3">{name}</td>
    <td className="p-3">{value.toString()}</td>
  </tr>
);

interface PropertiesComposition {
  Prop: typeof Prop;
}

export const Properties: React.FC<React.HTMLAttributes<HTMLDListElement>> &
  PropertiesComposition = ({ children, ...props }) => {
  return (
    <dl {...props} className={c("mb-0", props.className)}>
      {" "}
      {Children.map(children, (child, i) =>
        isPropComponent(child)
          ? cloneElement(child, {
              className: c(child.props.className, { "bg-light": i % 2 === 0 }),
            })
          : child
      )}{" "}
    </dl>
  );
};

const isPropComponent = (
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  child: any
): child is React.ReactElement | React.ReactPortal => {
  return (
    !!child &&
    typeof child === "object" &&
    "type" in child &&
    child.type === Prop
  );
};
```

## useId

```ts
import { useMemo } from "react";

// this is an adaptation of what mui does internally
// https://github.com/mui-org/material-ui/blob/v4.x/packages/material-ui/src/utils/unstable_useId.js
export const useId = (idOverride?: string) =>
  useMemo(
    () => idOverride || `id-${Math.round(Math.random() * 1e5)}`,
    [idOverride]
  );
```

```ts
// mui's version that works better with ssr ()
import * as React from "react";

/**
 * Private module reserved for @material-ui/x packages.
 */
export default function useId(idOverride) {
  const [defaultId, setDefaultId] = React.useState(idOverride);
  const id = idOverride || defaultId;
  React.useEffect(() => {
    if (defaultId == null) {
      // Fallback to this default id when possible.
      // Use the random value for client-side rendering only.
      // We can't use it server-side.
      setDefaultId(`mui-${Math.round(Math.random() * 1e5)}`);
    }
  }, [defaultId]);
  return id;
}
```
