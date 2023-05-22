# 파일이 있는지 확인

## File.exists()
## File.notExists()

```java


import java.io.File;
 
class Main
{
    // 파일이 존재하고 디렉토리가 아닌지 확인하는 메소드
    public static boolean isFileExists(File file) {
        return file.exists() && !file.isDirectory();
    }
 
    public static void main(String[] args)
    {
        String filePath = "C:\\doc.txt";
        File file = new File(filePath);
 
        if (isFileExists(file)) {
            System.out.println("File exists!!");
        }
        else {
            System.out.println("File doesn't exist or program doesn't have access " +
                            "to the file");
        }
    }
}

```

## File.isFile()
- 파일이 디렉토리인지 아닌지 사용

```java

import java.io.File;
 
class Main
{
    // 파일이 존재하는지 확인하는 메소드
    public static boolean isFileExists(File file) {
        return file.isFile();
    }
 
    public static void main(String[] args)
    {
        String filePath = "C:\\doc.txt";
        File file = new File(filePath);
 
        if (isFileExists(file)) {
            System.out.println("File exists!!");
        }
        else {
            System.out.println("File doesn't exist or program doesn't have access " +
                        "to the file");
        }
    }
}


```

## Java 7부터는 NIO

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
 
class Main
{
    public static void main(String[] args)
    {
        String filePath = "C:\\doc.txt";
 
        Path path = Paths.get(filePath);
 
        boolean exists = Files.exists(path);
        boolean notExists = Files.notExists(path);
        boolean isDir = Files.isDirectory(path);
 
        if (isDir) {
            System.out.println("File is a Directory");
        }
        else if (exists) {
            System.out.println("File exists!!");
        }
        else if (notExists) {
            System.out.println("File doesn't exist!!");
        }
        else {
            System.out.println("Program doesn't have access to the file!!");
        }
    }
}
```

## Files.isDirectory()

## Files.isRegularFile()

```java

import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
 
class Main
{
    public static void main(String[] args)
    {
        String filePath = "C:\\doc.txt";
 
        Path path = Paths.get(filePath);
        boolean exists = Files.isRegularFile(path);
 
        if (exists) {
            System.out.println("File exists!!");
        }
        else {
            System.out.println("File doesn't exist!!");
        }
    }
}

```

### https://www.techiedelight.com/ko/check-if-file-exists-java/