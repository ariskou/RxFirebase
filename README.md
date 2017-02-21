[![Hex.pm](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0) [![Platform](https://img.shields.io/badge/platform-android-green.svg)](http://developer.android.com/index.html)

# RxFirebase 2

RxJava 2 wrapper on Google's [Firebase for Android](https://www.firebase.com/docs/android/) library.


## Usage
Library provides set of static methods of classes:
- RxFirebaseAuth
- RxFirebaseUser
- RxFirebaseDatabase
- RxFirebaseStorage

### Authentication:

Sign in anonymously and get token:
```java
RxFirebaseAuth.signInAnonymously(FirebaseAuth.getInstance())
                .flatMap(x -> RxFirebaseUser.getToken(FirebaseAuth.getInstance().getCurrentUser(), false))
                .subscribe(token -> {
                    Log.i("RxFirebaseSample", "user token: " + token.getToken());
                }, throwable -> {
                    Log.e("RxFirebaseSample", throwable.toString());
                });
```

### Database:
Many thanks to @renanferrari for making it much more flexible.

You can simply observe values providing the Class of expected data like:
```java
RxFirebaseDatabase.observeSingleValue(reference.child("users").child("nick"), User.class)
            .subscribe(user -> {
                // process user value(nullable)
            });
```

or providing your own mapper between DataSnapshot and your data type:

```java
RxFirebaseDatabase.observeSingleValueEvent(reference.child("posts"),
                dataSnapshot -> {
                    // do your own mapping here
                    return new User();
                })
                .subscribe(user -> {
                    // process user value
                });
```

There are some pre-defined mappers to make things easier:

###### Observing list values
 ```java
 RxFirebaseDatabase.observeSingleValueEvent(reference.child("posts"), DataSnapshotMapper.listOf(BlogPost.class))
                 .subscribe(blogPost -> {
                     // process blogPost list item
                 });
 ```

###### Observing map values
 ```java
         RxFirebaseDatabase.observeSingleValueEvent(reference.child("posts"), DataSnapshotMapper.mapOf(BlogPost.class))
                 .subscribe(blogPostAsMapItem -> {
                     // process blogPost as key-value pair
                 });
 ```


### Storage

Download file from Firebase storage

```java
StorageReference storageRef = FirebaseStorage.getInstance().getReferenceFromUrl("gs://project-1125675579821020265.appspot.com");
RxFirebaseStorage.getFile(storageRef.child("README.md"), targetFile)
        .subscribe(snapshot -> {
            Log.i("RxFirebaseSample", "transferred: " + snapshot.getBytesTransferred() + " bytes");
        }, throwable -> {
            Log.e("RxFirebaseSample", throwable.toString());
        });
```

or download file as bytes array

```java
RxFirebaseStorage.getBytes(storageRef.child("README.md"), 1024 * 100)
                .subscribe(bytes -> {
                    Log.i("RxFirebaseSample", "downloaded: " + new String(bytes));
                }, throwable -> {
                    Log.e("RxFirebaseSample", throwable.toString());
                });
```


## Download

##### Gradle:
Step 1. Add the JitPack repository to your build file (root `build.gradle` file)

```groovy
allprojects {
  repositories {
    ...
    maven { url 'https://jitpack.io' }
  }
}
```

Step 2. Add the RxFirebase dependency along with the needed Firebase dependencies
```groovy
dependencies {
  compile 'com.google.firebase:firebase-auth:10.0.1'
  compile 'com.google.firebase:firebase-database:10.0.1'
  compile 'com.google.firebase:firebase-storage:10.0.1'
  compile 'com.github.ariskou:rxfirebase:master-SNAPSHOT'
}
```

##### Maven:
Step 1. Add the JitPack repository to your pom file

```
<repositories>
  <repository>
      <id>jitpack.io</id>
      <url>https://jitpack.io</url>
  </repository>
</repositories>
```

Step 2. Add the RxFirebase dependency
```
<dependency>
  <groupId>com.github.ariskou</groupId>
  <artifactId>rxfirebase</artifactId>
  <version>master-SNAPSHOT</version>
  <type>pom</type>
</dependency>
```


## License
    Copyright 2016 Nickolay Moskalenko
    Adaptation to RxJava 2 - Copyright 2017 Remous-Aris Koutsiamanis

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
