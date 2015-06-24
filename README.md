# gradle-mqsicreatebar-plugin
IBM Integration Bus MQSI Create Bar Gradle Plugin

Overview
--------

A gradle plugin that makes it easier to create bar files used for deploying onto IBM Integration Bus v9.0. The plugin executes the mqsicreatebar and mqsibaroverride commands. The plugin is useful in CI tools such as Jenkins.

Pre-Requisites
--------------

The mqsi environment must be initialized before the plugin can be used. For example, on windows you must run the gradle tasks within the IBM Integration Console (e.g. mqsiprofile.cmd).

Build
-----

This will build and install the plugin to the local maven repo.

./gradle install

Usage
-----

To use the plugin in your IIB project add the following into your build.gradle file

```groovy

// the version of your project
version=1.0

buildscript {
	repositories {
        maven {
			// location of where your maven repository resides
			url uri('file:/C:/dev/.m2/repository')	
        }
    }
    dependencies {
        classpath 'gradle.plugins.mqsicreatebar:gradle-mqsicreatebar-plugin:1.0'
    }
}

apply plugin: 'gradle.plugins.mqsicreatebar'
```

Currently this plugin only supports building IIB Application projects (e.g. mqsicreatebar -a <applicationName>). Support for Integration projects will be added in the near future. For each application project an equivalent bar file will be created. Support for packaging multiple applications in a single bar file is not supported.

The build.gradle file should be placed in the folder alongside your Application project (not the workspace folder). For example:

```
/workspace/
   /ApplicationA
      build.gradle
   /ApplicationB
     build.gradle
   /LIB1
   /LIB2
```   
   
In the above example you would need a build.gradle file for each Application project. When executing "gradle build" task in each project the following will be created.

```
/workspace/
   /ApplicationA
      build.gradle
      /build/
         ApplicationA-1.0.bar
   /ApplicationB
     build.gradle
      /build/
         ApplicationB-1.0.bar
   /IntegrationProjectA
      /build/
   /LIB1
   /LIB2
```

Tasks
-----

### Clean ###
Deletes all bar files from the build folder

```
gradle clean
```

### Build Application Projects ###
Creates a bar file from the build.gradle using the default properties from the project

```
gradle build
```

### Build Application with Overrides ###
Creates a bar file from the build.gradle and overrides the default properties in the broker.xml file. For example if we were building verion 1.0 of ApplicationA then:

```
gradle build -DoverridesFile=prod.properties
```

This will create the following bar file:

```
build/ApplicationA-1.0-prod.bar
```

An example of what a properties file could contain:

```
com.github.jamie3.MyFlow#Input Directory=properties

filenamePattern=abc.xml

```

### Build Integration Projects ###
Creates bar file(s) from an integration project. For an integration project you must create a build.config file alongside the build.gradle file. An example of the build.config file is shown below.

```
barFile{
	Flow1 {
		include=["flow/Flow1.msgflow"]
	}
	Flow2 {
		include=["flow/Flow2.msgflow"]
	}
}
```

Afterwards you can execute the build command

```
gradle build
```

This will produce two bar files: 

```
build/projectName-1.0-Flow1.bar
build/projectName-1.0-Flow2.bar
```

Note: Apply bar override for integration projects is not supported... yet.


### Debugging ###
Debugging can be enabled in the plugin as follows:


```
gradle <task> -Ddebug

```

