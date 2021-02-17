# Java

## Compiling
```bash
javac <list of all your java classes>
```
if you want to avoid having to list all of your classes you can use this
```bash
javac `find -name "*.java"`
```
if you want to specify some jars to add to the classpath
```bash
javac -cp "lib/some.jar" `find -name "*.java"`
```
if you want to specify the output directory
```bash
javac -d out/ `find -name "*.java"`
```

## Running
```bash
java <package path to main function> <args>
```
if you want to specify the directory of the compiled source
```bash
java -cp "out" my.package.main <args>
```