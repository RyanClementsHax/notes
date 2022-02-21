# Tools

## Get all methods on object

```js
const getMethods = (obj) => {
  let properties = new Set();
  let currentObj = obj;

  do {
    Object.getOwnPropertyNames(currentObj).map((item) => properties.add(item));
  } while ((currentObj = Object.getPrototypeOf(currentObj)));

  return [...properties.keys()].filter(
    (item) => typeof obj[item] === "function"
  );
};
```

## Otp logger

```js
// otpLogger.js
var totp = require("totp-generator");
var otpKey = "some key";

let generateToken = () => totp(otpKey);

let currentOtp = generateToken();
setInterval(() => {
  console.log(generateToken());
}, 250);
```

- run with `node ./otpLogger.js`

## Updating the same log line

- clears a line and resets cursor to the beginning

  ```js
  process.stdout.clearLine();
  process.stdout.cursorTo(0);
  ```

- moves the cursor to a positin, then clears everything to the right

  ```js
  process.stdout.cursorTo(position);
  process.stdout.clearLine(1);
  ```

## Formatting json

```js
JSON.stringify({ a: 1, b: 2, c: { d: 1, e: [1, 2] } }, null, "\t"); // tabs
JSON.stringify({ a: 1, b: 2, c: { d: 1, e: [1, 2] } }, null, 4); // 4 spaces
```

- the output needs to be rendered in a `<pre>` tag if you want the formatting to appear in the dom

## Converting case

- [converting camelCase to Title Case](https://stackoverflow.com/questions/4149276/how-to-convert-camelcase-to-camel-case)

## Copying from clipboard

- [examples](https://stackoverflow.com/questions/400212/how-do-i-copy-to-the-clipboard-in-javascript)
- there seems to be mixed apis and support across browsers
- it is recommended to use a library for this

## [Generating random strings](https://stackoverflow.com/questions/1349404/generate-random-string-characters-in-javascript)

## [Creating an array of numbers](https://stackoverflow.com/questions/3746725/how-to-create-an-array-containing-1-n)

## [Flattening an array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)

## [Interpolating a regex into another one](https://gist.github.com/elclanrs/3891362)

```js
const newRegex = new RegExp(
  `some pattern${regexToInterpolate
    .toString()
    /* remove some regex syntax that prevents it from being interpolated into another regex */
    .replace(/(^\/|\/$|\/([igm]+)$)/g, "")}`
);
```

## [Creating an array from 1 to n](https://stackoverflow.com/questions/3746725/how-to-create-an-array-containing-1-n)

- there are [plenty](https://stackoverflow.com/a/33352604) of ways to do this

## Format zip

```ts
const formatZip = (valueStr: string): string => {
  const nums = valueStr.match(/\d/g);
  if (nums === null) {
    return "";
  } else if (nums.length <= 5) {
    return nums.join("");
  } else if (5 < nums.length) {
    const firstFive = nums.slice(0, 5).join("");
    const nextFour = nums.slice(5, 9).join("");
    return `${firstFive}-${nextFour}`;
  }
  return "";
};
```

## [Javascript proxy as a rest client](https://gist.github.com/DavidWells/53518b3c12344952641dc81cc7599939)

```js
/* Using a JavaScript proxy for a super low code REST client */
// via https://dev.to/dipsaus9/javascript-lets-create-aproxy-19hg
// also see https://towardsdatascience.com/why-to-use-javascript-proxy-5cdc69d943e3
// also see https://github.com/fastify/manifetch
// also see https://github.com/flash-oss/allserver
// and https://gist.github.com/v1vendi/75d5e5dad7a2d1ef3fcb48234e4528cb

const createApi = (url) => {
  return new Proxy(
    {},
    {
      get(target, key) {
        return async function (id = "") {
          const response = await fetch(`${url}/${key}/${id}`);
          if (response.ok) {
            return response.json();
          }
          return Promise.resolve({ error: "Malformed Request" });
        };
      },
    }
  );
};

let api = createApi("https://swapi.co/api");

// 'get' request to https://swapi.co/api/people
let people = await api.people();

// 'get' request to https://swapi.co/api/people/1
let person = await api.people(1);

// ... any https://swapi.dev/ endpoint
```
