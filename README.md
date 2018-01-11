# [Codecov][1] Gradle Example

## Guide
### Travis Setup

Add to your `.travis.yml` file.
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
### Produce Coverage Reports
1. Add Jacoco Plugin to your `build.gradle`. [See here](https://github.com/codecov/example-gradle/blob/master/build.gradle#L5)
2. Set Jacoco to export xml. [See here](https://github.com/codecov/example-gradle/blob/master/build.gradle#L18-L23)
3. Execute your tests as normal
4. Call `gradle jacocoTestReport` to generate report. [See here](https://github.com/codecov/example-gradle/blob/65f88382659cf17c8693c3079941a12c8d004f03/circle.yml#L3)

## Caveats
### Private Repos
Add to your `.travis.yml` file.
```yml
after_success:
  - bash <(curl -s https://codecov.io/bash) -t uuid-repo-token
```
## Support
### FAQ
- Q: Do you support Multi-module projects?<br/>A:Update your parent (root) `build.gradle`:
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
### Contact
- Intercom (in-app messanger)
- Email: support@codecov.io
- Slack: slack.codecov.io
- [gh/codecov/support](https://github.com/codecov/support)

1. More documentation at https://docs.codecov.io
2. Configure codecov through the `codecov.yml`  https://docs.codecov.io/docs/codecov-yaml



[1]: https://codecov.io/
[2]: https://github.com/codecov/example-php/blob/master/.travis.yml#L15
[3]: https://github.com/codecov/example-php/blob/master/.travis.yml#L18
[4]: https://github.com/codecov/codecov-python
