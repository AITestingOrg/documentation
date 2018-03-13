Projects should follow well known and stablished code-style guides. 

In the case of Java code the [google style guide](https://google.github.io/styleguide/javaguide.html) is followed except that 4 spaces are used for indentation. 

Java projects should use checkstyle plugin for code style reports and checks.

Add the plugin to build graddle:

```groovy
apply plugin: 'checkstyle'
```

The plugin can be configured in a few ways:
```groovy
checkstyle {
	toolVersion = "8.7"
	configFile = new File(rootDir, "checkstyle.xml")
}

checkstyleMain {
	source = fileTree('src/main') {
		excludes = ['*.yml']
	}
}

checkstyleTest {
	source = fileTree('src/test') {
		excludes = ['*.yml']
	}
}
```

* toolVersion is used to specifie the version to use, by default it is an older version.

* configFile is used to specify the xml file with the rules to follow.

* the checkstyleMain and checkstyleTest can be used to customize these two tasks. Files to be excluded can be added.

* A modified version of google's javaguide can be found in [here](checkstyle.xml)