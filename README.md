# [Codecov][1] Gradle Example

1. Add Jacoco Plugin to your `build.gradle`. [See here](https://github.com/codecov/example-gradle/blob/master/build.gradle#L5)
1. Set Jacoco to export xml. [See here](https://github.com/codecov/example-gradle/blob/master/build.gradle#L18-L23)
1. Execute your tests as normal
1. Call `gradle jacocoTestReport` to generate report. [See here](https://github.com/codecov/example-gradle/blob/65f88382659cf17c8693c3079941a12c8d004f03/circle.yml#L3)
1. Call `bash <(curl -s https://codecov.io/bash)` to upload reports to Codecov. [See here](https://github.com/codecov/example-gradle/blob/65f88382659cf17c8693c3079941a12c8d004f03/circle.yml#L4)
  - Public project? Using TravisCI, CircleCI or AppVeyor? You're all set! No upload token required.
  - Otherwise please include your repository upload token. [See here](http://docs.codecov.io/v4.3.0/docs/about-the-codecov-bash-uploader#section-upload-token)

## Frequently Asked Questions

#### ❔ Do you support Multi-module projects?

Update your parent (root) `build.gradle`:

```groovy
allprojects {
    apply plugin: 'java'
    apply plugin: 'maven'
    apply plugin: 'jacoco'

    sourceCompatibility = 1.8
    targetCompatibility = 1.8

    repositories {
        mavenLocal()
        mavenCentral()
        jcenter()

        maven { url "http://repo1.maven.org/maven2/" }
    }
}

subprojects {
    dependencies {
        ...        
    }

    test.useTestNG()
}

task codeCoverageReport(type: JacocoReport) {
    executionData fileTree(project.rootDir.absolutePath).include("**/build/jacoco/*.exec")

    subprojects.each {
        sourceSets it.sourceSets.main
    }

    reports {
        xml.enabled true
        xml.destination "${buildDir}/reports/jacoco/report.xml"
        html.enabled false
        csv.enabled false
    }
}

codeCoverageReport.dependsOn {
    subprojects*.test
}
```

Update your `.travis.yml` file:

```yml
language: java
jdk:
  - oraclejdk8
before_script:
  - chmod +x gradlew
script:
  - ./gradlew check
  - ./gradlew codeCoverageReport
after_success:
  - bash <(curl -s https://codecov.io/bash)
```

No need to have anything else report-related in child modules 

[Credits for multi-module](https://csiebler.github.io/blog/2014/02/09/multi-project-code-coverage-using-gradle-and-jacoco/)

[1]: https://codecov.io/
