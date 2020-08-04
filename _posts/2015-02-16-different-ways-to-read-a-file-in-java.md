---
layout: post
date: 2015-02-16
title: "Different Ways To Read A File In Java"
categories: java
permalink: different-ways-to-read-a-file-in-java
---

I'm new to Java, and I needed to read a file. I Googled how to do this and well, I got so many variations on how to do it. But I couldn't find a page that had more than 2 ways of doing it. So I made this list.

**Note:** This is **Not** a complete list. Feel free to comment to add more.

## BufferedReader and FileReader

```java
BufferedReader br = new BufferedReader(new FileReader("file.txt"));
String str;
while((str = br.readLine()) != null){
    // do whatever with "str"
}
```

Requires: *java.io.BufferedReader* and *java.io.FileReader*
The [documentation](http://docs.oracle.com/javase/7/docs/api/java/io/FileReader.html) says that *FileReader* is meant for a stream of characters and states it's a "Convinience class for reading character files".

## FileInputStream
```java
Scanner fileInput = new Scanner(new FileInputStream("file.txt"));
while(fileInput.hasNextLine()){
    // do whatever with fileInput.nextLine()
}
```

Requires: *java.io.FileInputStream*
This is the one I use, it's simple and it works. Not really the most optimal solution though. According to the [documentation](http://docs.oracle.com/javase/7/docs/api/java/io/FileInputStream.html), *FileInputStream* reads the data as **bytes** and is mainly used for reading raw bytes such as from an image file. So I should really use something else but eh, it still works for reading a file.

## File

```java
Scanner fileInput = new Scanner(new File("file.txt");
while(fileInput.hasNextLine()){
    // do whatever with fileInput.nextLine()
}
```

Requires: *java.io.File*
Basically the same thing as above except we use *File* instead of *FileInputStream*.

## Files and Paths

```java
for (String line : Files.readAllLines(Paths.get("file.txt"))) {
    // do whatever with "line"
}
```

Requires: *java.nio.file.Files* and *java.nio.file.Paths*

## RandomAccessFile

```java
RandomAccessFile file = new RandomAccessFile("file.txt", "r");
for(String line = file.readLine(); line != null; line = file.readLine()){
    // Do whatever with "line"
}
```

Requires: *java.io.RandomAccessFile*
[RandomAccessFile](http://docs.oracle.com/javase/7/docs/api/java/io/RandomAccessFile.html) is pretty cool because it lets specify from where you want to start reading/writing data.

## InputStream, BufferedInputStream, and FileInputStream

```java
final InputStream in = new BufferedInputStream(new FileInputStream("file.txt"));
final byte[] buf = new byte[1000];
while (in.read(buf) != -1){
    // Do whatever with "buf"
}
in.close();
```

Requires: *java.io.InputStream*, *java.io.BufferedInputStream*, and *java.io.FileInputStream*
According to [this StackOverflow post](http://stackoverflow.com/a/10474077), this has the potential to read a 183 MB file in 250 ms.
