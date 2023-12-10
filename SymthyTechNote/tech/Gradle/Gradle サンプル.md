
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
  apply from: projectRoot.resolve("ossVersions.gradle")
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

task copyProperties() {
  doLast {
    copy {
      from "./release_static_files/config/"
      into "./config/"
      include "system.properties"
      include "user.properties"
      rename(/user.properties/, "user.properties.model")
    }
  }
}

def dependenciesFile = file('runtime_dependencies.txt')
task exportRuntimeDependences {
  doLast {
    if (dependenciesFile.exist()) {
      dependenciesFile.delete();
    }
    def writer = dependencies.newPrintWriter()
    def jarList = new ArrayList<>(configurations.runtimeClasspath as List)
    jarList.stream()
      .map(f -> f.name)
      .sorted()
      .each {
        name -> writer.print(name + "\n")
      }
    writer.close()
  }
}

dependencies {
  // 略
}

test {
  maxParallelForks = maxParallelForksForTesting
  jvmArgs "-Djunit.jupiter.extensions.autodetection.enabled=true",
  // ignore Illegal reflective access by retrofit2.Platform, can remove this after jdk14 or later
  "--add-opens=java.base/java.lang.invoke=ALL-UNNAMED"
}

def jacocoTargetProjects = allprojects.findAll {
  ["project_dir_name1", "sub_project_dir_name2"].contains(it.name)
}

gradle.projectsEvaluated {
  task jacocoAggregation(type: JacocoReport) {
    additionalClassDirs(files(jacocoTargetProjects.sourceSets.main.output.flatten()))
    additionalSourceDirs(files(jacocoTargetProjects.sourceSets.main.java.srcDirs.flatten()))
    executionData(jacocoTargetProjects.tasks.test.jacoco.destinationFile)
    reports {
      xml.required = false
      csv.required = false
      html.required = true
    }
  }
  jacocoTargetProjects.each {
    jacocoAggregation.depends it.test
  }
  test.finalizedBy jacocoAggregation
}

def dependenciesLib = Paths.get('dependenciesLib')
task copyDependencies() {
  doLast {
    copy {
      from configurations.runtimeClasspath
      into dependenciesLib
    }
  }
}
build.finalizedBy copyDependencies;

task cleanDependencies(type: Delete) {
  delete dependenciesLib
}
clean.finalizedBy cleanDependecies

java.nio.file.Path releaseDir = projectRoot.resolve('release')
java.nio.file.Path libsDir = releaseDir.resolve('libs')

task cleanRelease(type: Delete) {
  mustRunAfter build
  delete releaseDir
}
clean.finalizedBy cleanRelease

task release(dependsOn: [build, cleanRelease]) {
  mustRunAfter cleanRelease
  doLast {
    copy {
      from jar
      into releaseDir
    }
    copy {
      from configurations.runtimeClasspath
      into libsDir
    }
    copy {
      from 'release_static_files'
      into releaseDir
      exclude '**/.gitkeep'
    }
    copy {
      from 'definition/'
      into releaseDir.resolve('definition')
    }
    exec {
      "sh"
      args "-c", "command"
    }
  }
}

String raBallFileName = jar.archiveBaseName.get() + 'tar.gz';

task cleanTarBall(type: Delete) {
  mustRunAfter release
  delete tarBallFileName
}

clean.finalizedBy cleanTarBall
task tarBall(type: Tar, dependsOn: [release, cleanTarBall]) {
  compression = Compression.GZIP
  archiveFileName = tarBallFileName
  from releaseDir
  destinationDirectory = file('.')
}
```

```
def testRuntimeDirectory = projectRoot.resolve("test").resolve("runtime").toAbsolutePath()
task execute(type: JavaExec, dependsOn: [build]) {
  mainClass = "xxx"
  classpath = sourceSets.main.runtimeClasspath
  args("xxx", "xxx")
  environment("key", "value")
  jvmArgs("-Xms2G", "-Xmx10G", "-Duser.language=en", "-Duser.country=US"
    "-DlogDir=" + (findProperty("logDir") ?: "/path"),
    "-Xlog:gc:file=/path/xxx.log:time,uptime,pid:filecount=10,filesize=1024k",
    "-Dmy.product.config.execution.result.dir=${testRuntimeDirectory.resolve("result")},
    "-Djdk.tls.client.protocols=\"TLSv1.2\"")
}
```


buildConfig.gradle
```
ext {
  repoConfigFile = rootDir.toPath().resolve('gradle_scripts/repositoryConfig.gradle').toString()
  repoConfigFileExists = { ->
    return new Fuile(repoConfigFile).exists()
  }

  configureRepoFunction = rootDir.toPath().resolve('gradle_scripts/configureRepository.gradle').toString()
}
```


ossVersionsDefinition.gradle
```
ext {
  ossVersions = 
    [
      'lib_name': 'version'
    ]
}
```
