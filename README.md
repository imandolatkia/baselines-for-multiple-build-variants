# Android build variants baselines for linter
create baseline files for each build variants dynamically for android linter

# how to use?
1- add the following function to your app-level build.gradle file:

```Gradle
    Gradle gradle = getGradle()
    String tskReqStr = gradle.getStartParameter().getTaskRequests().toString()
    Pattern pattern
    String path
    String fileName = "lint-baseline"

    if (tskReqStr.contains("assemble"))
        pattern = Pattern.compile("assemble(\\w+)(Release|Debug)")
    else if (tskReqStr.contains("generate"))
        pattern = Pattern.compile("generate(\\w+)(Release|Debug)")
    else if (tskReqStr.contains("lint"))
        pattern = Pattern.compile("lint(\\w+)(Release|Debug)")

    if(pattern != null) {
        Matcher matcher = pattern.matcher(tskReqStr)

        if (matcher.find()) {
            path = matcher.group(1).toLowerCase() + matcher.group(2).toLowerCase()
            return "lint-baselines/${path}-${fileName}.xml"
        } else {
            return "lint-baselines/${fileName}.xml"
        }
    }
    return "lint-baselines/${fileName}.xml"
```
this function creates a specific path for each build variants.
you can customize the file name by changing the "fileName" variable.

</br>
2- add the following line to lintOption scop of your app-level build.gradle file:

```Gradle
 lintOptions {
        ...
        // Use (or create) a baseline file for issues that should not be reported
        baseline file("${getPath()}")
        ...
    }
```

</br>
3- run linter for each of build varients.

for example type the following command in the terminal tab of Android studio in the root of project: 

``` gradlew app:lintMyAppRelease```

```app``` = your module name

```MyAppRelease``` = your build varient

</br>
4- Done

