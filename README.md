# Baselines For Multiple Build Variants (For Android Lint)
Create android lint baselines dynamically for each build variants.
<br/><br/>

![2222222](https://user-images.githubusercontent.com/6734608/132840884-700fc464-b960-4000-a79d-b3853ddc38cc.jpg)


# 1- What is android lint?
Lint easy learning:  https://medium.com/swlh/what-is-android-lint-17fa0d87abb2

Lint official google doc: https://developer.android.com/studio/write/lint
<br/><br/>

## What is Baseline?
According to google doc: You can take a snapshot of your project's current set of warnings, and then use the snapshot as a baseline for future inspection runs so that only new issues are reported. The baseline snapshot lets you start using lint to fail the build without having to go back and address all existing issues first.
To create a baseline snapshot, modify your project's build.gradle file as follows.
```Groovy
android {
    lintOptions {
        baseline file("lint-baseline.xml")
    }
}
```
<br/><br/>

# 2- Now, what is the problem?
If you have multiple build variants in your app and have specific codes for them, you can't create a separate baseline for them.
Because, according to the google doc, you can define only one baseline in the project.
It means that every time you change build variants, you may see a new lint error that is not in the baseline.
<br/><br/>


# 3- So, how solve the problem?
1- add the following function to your app-level build.gradle file:

```Gradle
def getPath() {
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
}
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

