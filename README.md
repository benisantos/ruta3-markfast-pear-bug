
# ruta3-markfast-pear-bug

This is a sample project to reproduce a likely bug with Ruta 3 MARKFAST/TABLE in a PEAR context.

## Description

When running an AAE in a PEAR context, if the **first** Ruta script after the CAS creation containts a MARKFAST (same for MARKTABLE), that action doesn't work.

The problem seems to be coming from the method `RutaStream#moveToFirst` when it's called from `TreeWordList.find`; in that case, the underlying iterator is not properly reset.

## Steps to reproduce the problem

```sh
# 1) Generate the pear in target/ruta3-markfast-pear-bug-0.1.0-SNAPSHOT.pear
mvn package

# 2) Install PEAR and run the AAE
cp=~/.m2/repository/org/apache/uima/uimaj-core/3.2.0/uimaj-core-3.2.0.jar
cp=$cp:~/.m2/repository/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar
cp=$cp:~/.m2/repository/org/slf4j/slf4j-simple/1.7.25/slf4j-simple-1.7.25.jar
cp=$cp:target/ruta3-markfast-pear-bug-0.1.0-SNAPSHOT.jar
java -cp $cp ruta3.PearRunner target/ruta3-markfast-pear-bug-0.1.0-SNAPSHOT.pear
```

It outputs something like:

```
ERROR! No FooBar annotations!
```

**Note**: It was tested with AdoptOpenJDK 1.8.0_252 and Apache Maven 3.3.9.


## Workaround to "fix" it

If other Ruta script (for instance, an empty one) runs before the script that has the MARKFAST, the problem goes away. To probe that:

* Uncomment the Dummy.ruta script in `src/main/resources/FooBarPipeline.xml` and try again (rebuild the pear and launch the `PearRunner`).

Now the MARKFAST works and the output is something like:

```
FooBar detected! foo
FooBar detected! bar
```
