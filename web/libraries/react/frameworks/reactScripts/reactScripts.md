# React Scripts

## Profiling Jest tests

Naively trying to run a debugger against `react-scripts test` will dissapoint you because this script runs `jest` off process.

I'm not sure how to profile spawned processes, but setting up the app to run `jest` directly worked for me.

The following are the steps I took to profile my Jest tests

1. run `react-scripts eject`
   - don't worry, we won't commit these changes
2. revert all changes to `package.json` except for the `"jest"` and `"babel"` fields that were created
3. install `react-dev-utils`
4. fix your config loading problems
   - I've had big problems loading in the `.env` files like `react-scripts` did
   - I didn't spend too much time on it because all I wanted to do was profile and I wasn't going to commit the changes anyway
   - I just hard coded the values in the source code and went on my way
   - There is probably a way to get around this, but like I said, I didn't care too much to find out at the time
5. run `CI=true node --inspect-brk node_modules/.bin/jest --runInBand`
   - `CI=true` helps the profile to only focus on the test code
   - `--runInBand` makes the tests run sequentially
   - after running this, you'll notice that it begins to wait for a debugger to connect
6. open up chrome browser dev tools and click on the Node.js icon
   - this will open up the profiling tools
7. go to the profiler tab, select the vm that's running, and start profiling
   - this should make the tests begin to run
8. after the tests finish running, stop the profiling
9. wait for the profile results to appear
   - this may take a minute or two if the profile ran for a long time
