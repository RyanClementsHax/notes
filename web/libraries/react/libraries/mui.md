# MUI

## [Programatic focus](https://github.com/mui-org/material-ui/issues/9659#issuecomment-704742095)

- this isnt supported very well
- this hack works decently

```jsx
import React, { useRef, useState } from "react";
import { render } from "react-dom";
import { Checkbox, Button } from "@material-ui/core";

const App = () => {
  const [node, setNode] = useState();
  const [action, setAction] = useState();

  return (
    <React.Fragment>
      <Checkbox inputRef={setNode} action={setAction} />
      <Button
        onFocus={() => {
          node.focus();
          action.focusVisible();
        }}
      >
        Set focus
      </Button>
    </React.Fragment>
  );
};

render(<App />, document.getElementById("root"));
```
