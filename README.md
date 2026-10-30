# Gradle does not detect a Microsoft Build of OpenJDK installed by its MSI

On Windows, the Microsoft Build of OpenJDK's MSI registers each install under
`HKLM\SOFTWARE\Microsoft\JDK\<version>\hotspot\MSI` (value `Path`), and Gradle's toolchain auto-detection does not read
that key. This build's daemon JVM criteria (`gradle/gradle-daemon-jvm.properties`) ask for JDK 25 with no download URL,
and its wrapper runs the Gradle nightly of `master`.

On Windows, with the Microsoft Build of OpenJDK 25 installed by its MSI (`winget install Microsoft.OpenJDK.25`) and
`JAVA_HOME` naming another JDK, such as the Microsoft Build of OpenJDK 17:

```
git clone https://github.com/YevheniiKotyrlo/gradle-microsoft-jdk-detection
cd gradle-microsoft-jdk-detection
gradlew.bat -g %TEMP%\empty-gradle-user-home help
```

The empty Gradle user home keeps a JDK Gradle provisioned earlier out of the detection.

Expected: the daemon starts on the installed JDK 25. Seen:

```
Unable to download toolchain matching the requirements ({languageVersion=25, vendor=any vendor, implementation=vendor-specific, nativeImageCapable=false}), due to: No defined toolchain download url for WINDOWS on x86_64 architecture.
```

The workflow in `.github/workflows/run-reproducer.yml` runs the same on `windows-latest`.
