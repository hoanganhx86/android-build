
#1 Make sure you include the fabric maven repo, and the fabric gradle to the classpath, like so:

_build.gradle_
```gradle
buildscript {
    repositories {
        maven { url 'https://maven.fabric.io/public' }
    }

    dependencies {
        classpath 'io.fabric.tools:gradle:1.+'
    }
}
```

#2 Add the fabric plugin AFTER the com.android.application plugin

```gradle
apply plugin: 'io.fabric'
```

#3 Add the Fabric Maven repository below the apply plugin command

```gradle
repositories {
    maven { url 'https://maven.fabric.io/public' }
}

```

#4 Add the release notes, and the name of the distribution group

```gradle
def releaseNotes = 'git log --pretty=format:"%s" --since "yesterday"'.execute([], project.rootDir).text.trim()
ext {
    betaDistributionReleaseNotes = releaseNotes
    betaDistributionGroupAliases = "debug"
}
```

#5 Add the crashlytics sdk to the gradle dependencies

```gradle
compile('com.crashlytics.sdk.android:crashlytics:+@aar') {
    transitive = true;
}
```

#6 Create a fabric.properties file in the app/ directory, include the info:

```ini
apiSecret=YOUR_BUILD_SECRET
apiKey=YOUR_API_KEY
```

#7 Include the API key in your AndroidManifest.xml

```xml
<meta-data
            android:name="io.fabric.ApiKey"
            android:value="API_KEY"/>

```

#8 Start crashlytics in your Application subclass onCreate

```java
private void setupFabric() {
    // Set up Crashlytics, disabled for debug builds
    Crashlytics crashlyticsKit = new Crashlytics.Builder()
            .core(new CrashlyticsCore.Builder().disabled(BuildConfig.DEBUG).build())
            .build();

    // Initialize Fabric with the debug-disabled crashlytics.
    Fabric.with(this, crashlyticsKit);
}
```
