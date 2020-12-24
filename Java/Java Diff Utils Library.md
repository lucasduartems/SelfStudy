## java-diff-utils library

### Intro

The [java-diff-utils](https://github.com/java-diff-utils/java-diff-utils) library (Apache 2.0 license) can be used to generate diffs between an *original* and a *revised* file.

Using the files below as examples, we can use the library to generate a Markdown table that compares both files in a side-by-side fashion, highlighting all the differences.

### Gradle dependency to add
```gradle
compile group: 'io.github.java-diff-utils', name: 'java-diff-utils', version: '4.0'
```

### Files to be compared

#### log1.txt
```
*A:ALA-1>config>router>if# info detail  
----------------------------------------------  
address 10.10.10.103/32 broadcast host-ones  
tos-marking-state trusted  
no local-proxy-arp  
no proxy-arp  
icmp  
mask-reply  
redirects 100 10  
unreachables 100 10  
ttl-expired 100 10  
exit  
no mac  
no ntp-broadcast  
no cflowd  
no shutdown
```

#### log2.txt
```
*A:ALA-1>config>router>if# info detail  
----------------------------------------------  
address 10.10.10.104/32 broadcast host-ones  
no description  
no arp-timeout  
no allow-directed-broadcasts  
tos-marking-state trusted  
no local-proxy-arp  
no proxy-arp  
icmp  
mask-reply  
redirects 100 10  
unreachables 100 10  
ttl-expired 100 10  
exit  
no shutdown
```

### Java code

```java
package com.example;

import com.github.difflib.algorithm.DiffException;
import com.github.difflib.text.DiffRowGenerator;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.List;

public class Diff {

    public static final String ORIGINAL_FILE = "log1.txt";
    public static final String REVISED_FILE  = "log2.txt";

    public void generateMarkdownTableWithDiff(File original, File revised) throws Exception {
        // Read file contents into lists of Strings
        List<String> originalFileContents = Files.readAllLines(original.toPath());
        List<String> revisedFileContents  = Files.readAllLines(revised.toPath());

        DiffRowGenerator diffGenerator = DiffRowGenerator.create()
                                                         .showInlineDiffs(true)
               /* Highlight diffs on single chars  */    .inlineDiffByWord(false)
               /* Markdown bold to highlight diffs */    .oldTag(f -> "**")
                                                         .newTag(f -> "**")
                                                         .build();

        // Generate Markdown table header
        System.out.println("|Original|Revised|");
        System.out.println("|--------|-------|");

        // Generate Markdown table lines
        diffGenerator.generateDiffRows(originalFileContents, revisedFileContents).forEach(
            row -> System.out.println("|" + row.getOldLine() + "|" + row.getNewLine() + "|")
        );
    }

    public static void main(String[] args) throws Exception {
        new Diff().generateMarkdownTableWithDiff(
            new File(ORIGINAL_FILE), new File(REVISED_FILE)
        );
    }

}
```

### Generated output (raw)
```
|Original|Revised|
|--------|-------|
|*A:ALA-1&gt;config&gt;router&gt;if# info detail|*A:ALA-1&gt;config&gt;router&gt;if# info detail|
|----------------------------------------------|----------------------------------------------|
|address 10.10.10.10**3**/32 broadcast host-ones|address 10.10.10.10**4**/32 broadcast host-ones|
||**no description**|
||**no arp-timeout**|
||**no allow-directed-broadcasts**|
|tos-marking-state trusted|tos-marking-state trusted|
|no local-proxy-arp|no local-proxy-arp|
|no proxy-arp|no proxy-arp|
|icmp|icmp|
|mask-reply|mask-reply|
|redirects 100 10|redirects 100 10|
|unreachables 100 10|unreachables 100 10|
|ttl-expired 100 10|ttl-expired 100 10|
|exit|exit|
|**no mac**||
|**no ntp-broadcast**||
|**no cflowd**||
|no shutdown|no shutdown|
```

### Generated output (rendered Markdown)
|Original|Revised|
|--------|-------|
|*A:ALA-1&gt;config&gt;router&gt;if# info detail|*A:ALA-1&gt;config&gt;router&gt;if# info detail|
|----------------------------------------------|----------------------------------------------|
|address 10.10.10.10**3**/32 broadcast host-ones|address 10.10.10.10**4**/32 broadcast host-ones|
||**no description**|
||**no arp-timeout**|
||**no allow-directed-broadcasts**|
|tos-marking-state trusted|tos-marking-state trusted|
|no local-proxy-arp|no local-proxy-arp|
|no proxy-arp|no proxy-arp|
|icmp|icmp|
|mask-reply|mask-reply|
|redirects 100 10|redirects 100 10|
|unreachables 100 10|unreachables 100 10|
|ttl-expired 100 10|ttl-expired 100 10|
|exit|exit|
|**no mac**||
|**no ntp-broadcast**||
|**no cflowd**||
|no shutdown|no shutdown|

### References
[java-diff-utils Wiki, Examples](https://github.com/java-diff-utils/java-diff-utils/wiki/Examples)
