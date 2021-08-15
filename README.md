# Amplituda<img align="right" src="https://github.com/lincollincol/Amplituda/blob/master/img/amplituda_preview.png" width="250" height="250">  

![GitHub release (latest by date)](https://img.shields.io/github/v/release/lincollincol/Amplituda)
![GitHub](https://img.shields.io/github/license/lincollincol/Amplituda)

![GitHub followers](https://img.shields.io/github/followers/lincollincol?style=social)
![GitHub stars](https://img.shields.io/github/stars/lincollincol/Amplituda?style=social)
![GitHub forks](https://img.shields.io/github/forks/lincollincol/Amplituda?style=social)

## What is Amplituda?
Amplituda - an android library based on FFMPEG which process audio file and provide an array of samples. Based on the processed data, you can easily draw custom waveform using the value of the processed data array as the height of the single column.  
Average processing time is equal to 1 second for audio with duration **3 min 20 seconds** and **1 hour** audio will be processed in approximately 20 seconds.

You can also use <a href="https://github.com/massoudss/waveformSeekBar">WaveformSeekBar</a> library which is fully compatible with Amplituda 
<p align="center">
  <img src="https://github.com/lincollincol/Amplituda/blob/master/img/waveform_1.jpg" width="250" height="50"/>&#10240 &#10240
  <img src="https://github.com/lincollincol/Amplituda/blob/master/img/waveform_2.jpg" width="250" height="50"/>
  <br/><br/>
  <img src="https://github.com/lincollincol/Amplituda/blob/master/img/waveform_3.jpg" width="250" height="50"/>&#10240 &#10240
  <img src="https://github.com/lincollincol/Amplituda/blob/master/img/waveform_4.jpg" width="250" height="50"/>
   <br/><br/>
  <img src="https://github.com/lincollincol/Amplituda/blob/master/img/waveform_5.jpg" width="250" height="50"/>&#10240 &#10240
</p>

## How to use Amplituda? 
#### Example
``` java

/* Step 1: create Amplituda */
Amplituda amplituda = new Amplituda(context);

/* Step 2: process audio and handle result */
amplituda.processAudio("/storage/emulated/0/Music/Linc - Amplituda.mp3")
        .compress(1) // Optional call. Parameter `1` means that you want 1 amplitude per second
        .get(result -> {
            List<Integer> amplitudesData = result.amplitudesAsList();
            List<Integer> amplitudesForFirstSecond = result.amplitudesForSecond(1);
            long duration = result.getAudioDuration(AmplitudaResult.DurationUnit.SECONDS);
            String source = result.getAudioSource();
            InputAudio.Type sourceType = result.getInputAudioType();
            // etc
        }, exception -> {
            if(exception instanceof AmplitudaIOException) {
                System.out.println("IO Exception!");
            }
        });

/* And that's all! You can read full documentation below for more information about Amplituda features */

``` 

### Full documentation 
#### • Process audio
``` java
Amplituda amplituda = new Amplituda(context);

/**
 * AmplitudaProcessingOutput<T> - wrapper class for Amplituda processing result.
 * This class stores the output audio processing data
 * and provides functions to obtain the result
 * Generic type T - audio source type: File, String (URL or path), Integer (res/raw) 
 */
AmplitudaProcessingOutput<T> processingOutput;

/* Process audio */

// Local audio file
processingOutput = amplituda.processAudio(new File("/storage/emulated/0/Music/Linc - Amplituda.mp3"));

// Path to local audio file
processingOutput = amplituda.processAudio("/storage/emulated/0/Music/Linc - Amplituda.mp3");

// URL audio
processingOutput = amplituda.processAudio("https://audio-url-example.com/amplituda.mp3");

// Resource audio
processingOutput = amplituda.processAudio(R.raw.amplituda);

/** 
 * Compress result data (optional)
 * The output data can contain a lot of samples. 
 * For example: 
 *  - 5-second audio can contain 100+ samples
 *  - 3-minute audio can contain 7000+ samples
 * You can call compress() and pass the number of preferred samples per second as a parameter.
 */
processingOutput.compress(1);

```

#### • Handle result and errors
``` java
AmplitudaProcessingOutput<T> processingOutput;
// . . . process audio . . .

/**
 * AmplitudaResult<T> - wrapper class for the final result
 * This class also provides many functions to format result: List<Integer>, String etc
 * and info about input audio: 
 *  - Input audio source: path to audio/resource or url. 
 *  - Source type according to input audio: FILE, PATH, URL or RESOURCE 
 *  - Audio duration
 * Generic type T - audio source type: File, String (URL or path), Integer (res/raw)
 */
 AmplitudaResult<T> result;
 
 /**
  * After processing you can get the result by calling the function get() 
  * This function has multiple overloads:
  *  - void get(successListener, errorListener)
  *  - void get(successListener)
  *  - AmplitudaResult<T> get(errorListener)
  *  - AmplitudaResult<T> get(successListener)
  */
 
// Overload #1: result as a callback with error listener
processingOutput.get(result -> { 
        /* handle result here */ 
    }, exception -> { 
        /* handle errors here */ 
    });

// Overload #2: result as a callback without error listener
processingOutput.get((AmplitudaSuccessListener<T>) result -> { 
    /* handle result here */ 
});

// Overload #3: result as a returned object with error listener
result = processingOutput.get((AmplitudaException exception) -> { 
    /* handle errors here */ 
});

// Overload #4: result as a returned object without error listener
result = processingOutput.get();

/**
 * Exceptions. More info about exceptions here:
 * https://github.com/lincollincol/Amplituda/tree/master/app/src/main/java/linc/com/amplituda/exceptions
 */
processingOutput.get((AmplitudaException exception) -> {
    if(exception instanceof AmplitudaIOException) {
        // Handle io exceptions
    } else if(exception instanceof AmplitudaProcessingException) {
        // Handle processing exceptions
    } else {
        exception.printStackTrace();
    }
});

```

#### • Format result


``` java
/**
 * Format result. As written earlier, AmplitudaResult
 * has many functions for formatting the result
 */
 AmplitudaResult<T> result;
 
 // Get result as list:
 List<Integer> samples = result.amplitudesAsList(); 
 System.out.println(Arrays.toString(samples.toArray()));
 // Output: [0, 0, 0, 0, 0, 5, 3, 6, . . . , 6, 4, 7, 1, 0, 0, 0]
 
 // Get result as list only for second `1`
 List<Integer> samplesForFirstSecond = result.amplitudesForSecond(1);
 System.out.println(Arrays.toString(samples.toArray()));
 // Output: [0, 0, 5, . . . 6, 4, 0]

// Get result as json format String
System.out.println(result.amplitudesAsJson());
// Output: [0, 0, 0, 0, 0, 5, 3, 6, . . . , 6, 4, 7, 1, 0, 0, 0]

// Get result as single line sequence format String (horizontal String)
System.out.println(result.amplitudesAsSequence(
        AmplitudaResult.SequenceFormat.SINGLE_LINE)
);
// Output: 0 0 0 0 0 5 3 6 . . . 6 4 7 1 0 0 0

// Get result as single line sequence format String with custom delimiter `*` (horizontal String)
System.out.println(result.amplitudesAsSequence(
        AmplitudaResult.SequenceFormat.SINGLE_LINE, " * ")
);
// Output: 0 * 0 * 0 * 0 * 0 * 5 * 3 * 6 * . . . 6 * 4 * 7 * 1 * 0 * 0 * 0

// Get result as new line sequence format String  
System.out.println(result.amplitudesAsSequence(
        AmplitudaResult.SequenceFormat.NEW_LINE)
);
/* Output: 
    0 
    0 
    5 
    3 
    . . . 
    6 
    1 
    0 
    0
    */

```

#### • Enable Amplituda logs (optional)
``` java
Amplituda amplituda = new Amplituda(context);

// Use default android Log constants to set priority. The second parameter - enable or disable logs. 
// Amplituda logs are disabled by default
amplituda.setLogConfig(Log.DEBUG, true);
```

### Permissions
Add permissions to Manifest.xml file in your app and grant it, before using Amplituda
``` xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.INTERNET" />
```
### Reduce size
Add ``` android:extractNativeLibs="false" ``` to application in the Manifest.xml

``` xml
<application
      . . .
    android:extractNativeLibs="false"
      . . . >
    <activity . . ./>
</application>
```

## Download
### Gradle
``` groovy
allprojects {
  repositories {
    maven { url 'https://jitpack.io' }
  }
}
```
``` groovy
dependencies {
  implementation 'com.github.lincollincol:Amplituda:2.1.0'
}
```

### Maven
``` xml
<repositories>
  <repository>
    <id>jitpack.io</id>
    <url>https://jitpack.io</url>
  </repository>
</repositories>
```
``` xml
<dependency>
  <groupId>com.github.lincollincol</groupId>
  <artifactId>Amplituda</artifactId>
  <version>2.1.0</version>
</dependency>
```

## Feedback
<a href="https://mail.google.com">andriy.serb1@gmail.com</a>

# License
```
   Copyright 2020-present lincollincol

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
```
