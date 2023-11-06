
build.gradle
```gradle
import java.nio.file.Paths

plugins {
  id 'java'
  id 'checkstyle'
  id "com.github.spotbugs" version "5.0.5"
  id "jacoco"
  id 'org.checkerframework' version '0.5.2'
}

group 'com.xxx.xxx.xxx'
jar.archiveBaseName 'xxx'

def projectRoot = project.getRootDir().toPath()
ext.fromRootDirectory = {
  projectRoot.resolve(it.toSting()).toFile()
}
sourceCompatibility = 1.11

allprojects {
  apply from: projectRoot.resolve("ossVersionDef.gradle")
  def aProject = it
  repositories {
    apply from: rootDir.toPath().resolve('buildConfig.gradle').toString()
    if (repoConfigFileExsits()) {
      apply from: repoConfigFile
      apply from: configureRepoFunction
      if (aProject == rootProject) {
        println "Apply ${mavenCentralMirror} as a repository for library repository"
      }
      configureMavenUrl(owner, "${mavenCentralMirror}")
    }
    mavenCentral()
  }

  plugins.withId('checkstyle', {
    checkstyleMain {
      configFile = fromRootDirectory('./config/checkstyle/google_checks.xml')
    }
    checkstyleTest {
      configFile = fromRootDirectory('./config/checkstyle/google_checks.xml')
    }
    checkstyle {
      toolVersion 'x.x';
    }
  })

  plugins.withId('com.github.spotbugs', {
    spotbugs {
      excludeFilter = fromRootDirectory('./config/findbugs/fingbugs-exclude.xml')
    }
    spotbugsTest {
      excludeFilter = fromRootDirectory('./config/findbugs/findbugs-test-exclude.xml')
    }
  })

  def project = it
  plugins.withId('java', {
    test {
      useJUnitPlatform {
        excludeTags "heavy", "real-env"
      }
    }
    project.tasks.register('fullTest'), Test.class, {
      useJUnitPlatform {
        excludeTags "real-env"
      }
    }
  })

  plugins.withId('jacoco'. {
    jacocoTestReport {
      enabled = false
    }
    jacoco {
      toolVersion = "x.x.x"
    }
  })

  plugins.withId('org.checkerframework') {
    checkerFramework {
      checkers = [
        'org.checkerframework.checker.nullness.NullnessChecker'
      ]
      extraJavaArgs = [
        '-AsuppressWarnings=keyfor'
      ]
      excludeTests = true
    }
  }

  afterEvaluate {
    if (plugins.findPlugin(JavaPlugin.class) != null && plugins.findPlugin(JacocoPlugin.class) != null) {
      test.finalizedBy jacocoTestReport
      jacocoTestReport.dependsReport
      fullTest.finalizedBy jacocoTestReport
      jacocoTestReport {
        excutionData jacocoTestReport
      }
    }
   
    tasks.withType(JavaCompile) {
      options.compilerArgs << "-Xlint:all" << "-Xdiags:verbose" << "-Werror" << "-Xlint:-processing"
      options.encoding = 'UTF-8'
    }

    tasks.withType(com.github.spotbugs.snom.SpotBugsTask) {
      reports {
        xml.enabled = false
        html.enabled = true
      }
    } 
    spotbugs {
      toolVersion = 'x.x.x'
    }
  }

  ext {
    maxParallelForksForTesting
  }
}

```