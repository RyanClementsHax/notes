# Tools

## Get all methods on object
```js
const getMethods = (obj) => {
    let properties = new Set()
    let currentObj = obj

    do {
        Object.getOwnPropertyNames(currentObj)
            .map((item) => properties.add(item))
    } while ((currentObj = Object.getPrototypeOf(currentObj)))

    return [...properties.keys()]
        .filter((item) => typeof obj[item] === 'function')
}
```

## Otp logger
```js
// otpLogger.js
var totp = require('totp-generator')
var otpKey = "some key"

let generateToken = () => totp(otpKey)

let currentOtp = generateToken()
setInterval(() => {
  console.log(generateToken())
}, 250)
```
- run with `node ./otpLogger.js`

## Updating the same log line
- clears a line and resets cursor to the beginning
  ```js
    process.stdout.clearLine()
    process.stdout.cursorTo(0)
  ```
- moves the cursor to a positin, then clears everything to the right
  ```js
    process.stdout.cursorTo(position)
    process.stdout.clearLine(1)
  ```