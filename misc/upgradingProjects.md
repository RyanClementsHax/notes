# Upgrading Projects

## Tips
- Sometimes you can delete dependencies instead of going through the hastle of upgrading them
- When upgrading a large amount of version numbers, it can be beneficial to upgrade straight to the newest version
- It can be helpful to bring in domain experts in the areas you are upgrading
    - Ex: When upgrading from Java 8 to Java 12, the domain expert we called on was able to walk us through the new module feature that was causing us some headaches
- Do the upgrade on a feature branch
- Never assume that every line of code in the repo is correct
    - Ex: When upgrading, I once found a preexisting faulty config file that was causing errors in a newer version of a package
- Sometimes you have to bite the bullet and read tons of documentation to figure out how your tech works

## Upgrading Java Projects
- Just because it doesn't show up in the maven dependency tree, it doesn't mean that it isn't a dependency
    - The dependency tree output is dependent on flags/options you pass in
    - [This](https://www.iditect.com/how-to/59294690.html) has a useful script to identify dependencies
        ```bash
        mvn dependency:copy-dependencies -DincludeScope=test -DoutputDirectory=deps
        for i in deps/*.jar; do if unzip -l $i| grep -q java.awt; then echo $i; fi ; done
        ```
- Consider moving to C#
    - "I would argue that Java is not a serious language" - Alin Dobra, PhD

## Rewrites
- sometimes you need to reimplement bugs to keep consistency
- use the old version for verification
  - Shopify sent the requests to both versions during in production during their rewrite and recorded the diffs between the two
- make sure to get a very short feedback loop for verification
- start with a tiny scope for proof of concept
- make it easy and frictionless to transfer
  - aim that your new system is the path of least resistance
- figure out what your trip wires are that will cause you to bail your rewrite like not being able to catch up the project with internal changes or production problems