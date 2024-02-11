# Week 5 Lab Report - Matt Fan

## Part 1 - Bugs
### Failure-inducing input
```java
@Test
public void testAverageWithoutLowest() {
    double[] input1 = {1, 1, 2, 2, 3, 3, 4, 4, 5};
    assertEquals(ArrayExamples.averageWithoutLowest(input1), 3.0, 0.001);
}
```
### Non-failure-inducing input
```java
@Test
public void testAverageWithoutLowestPassing() {
    double[] input1 = {7, 6, 5, 4, 3, 2, 1};
    assertEquals(ArrayExamples.averageWithoutLowest(input1), 4.5, 0.001);
}
```
### Symptom
```
forkpoweroutlet@aaaaaaaaaa:~/lab3$ javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
forkpoweroutlet@aaaaaaaaaa:~/lab3$ java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore ArrayTests
JUnit version 4.13.2
..E....
Time: 0.017
There was 1 failure:
1) testAverageWithoutLowest(ArrayTests)
java.lang.AssertionError: expected:<2.875> but was:<3.0>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:555)
        at org.junit.Assert.assertEquals(Assert.java:685)
        at ArrayTests.testAverageWithoutLowest(ArrayTests.java:36)

FAILURES!!!
Tests run: 6,  Failures: 1
```

### Bug and fix
#### Before
```java
static double averageWithoutLowest(double[] arr) {
    if(arr.length < 2) { return 0.0; }
    double lowest = arr[0];
    for(double num: arr) {
        if(num < lowest) { lowest = num; }
    }
    double sum = 0;
    for(double num: arr) {
        if(num != lowest) { sum += num; }
    }
    return sum / (arr.length - 1);
}
```
#### After
```java
static double averageWithoutLowest(double[] arr) {
    if(arr.length < 2) { return 0.0; }
    double lowest = arr[0];
    double sum = 0;
    for(double num: arr) {
        if(num < lowest) { lowest = num; }
        sum += num;
    }
    return (sum - lowest) / (arr.length - 1);
}
```
The bug is that the method skips all occurences of the lowest value instead of simply dropping one occurence, and this only shows itself in arrays with duplicate values.
```java
if(num != lowest) { sum += num; }
```
Here, sum will never be incremented if num happens to be equal to the lowest value, even if it is a duplicate.
The first test has two of the lowest value, 1, and one of those 1's is being erroneously excluded from the sum. The second test has unique values and behaves as expected.
The fix I implemented was to simply subtract off the lowest value at the end when calculating the mean. By doing so only once, duplicates are properly handled with the added benefit of only having to loop once. After doing so, tests pass:
```
forkpoweroutlet@aaaaaaaaaa:~/lab3$ javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
forkpoweroutlet@aaaaaaaaaa:~/lab3$ java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore ArrayTests
JUnit version 4.13.2
......
Time: 0.008

OK (6 tests)
```

## Part 2 - Researching Commands
### `grep -c`
This option forgoes normal output for just counting occurences instead ([GNU manual](https://www.gnu.org/software/grep/manual/grep.html)).
#### Ex. 1
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -c "al Qaeda" ./911report/chapter-2.txt
55
```
The command searches for the string `"al Qaeda"` in `technical/911report/chapter-2.txt`, but instead of highlighting occurences, it counts the number of occurences. This could be useful for finding the number of sequences in a file or counting duplicates in a list.
#### Ex. 2
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -c "intoxication" ./government/Alcohol_Problems/*
./government/Alcohol_Problems/DraftRecom-PDF.txt:0
./government/Alcohol_Problems/Session2-PDF.txt:9
./government/Alcohol_Problems/Session3-PDF.txt:3
./government/Alcohol_Problems/Session4-PDF.txt:3
```
The command searches for the phrase `"intoxication"` across multiple files. This could be used to determine which files contain the highest occurences of a target sequence, or filter out those based on a certain threshold.

### `grep -q`
The Q flag suppresses all standard output and uses return codes to indicate the search term's presence. ([LinuxHint](https://linuxhint.com/use-grep-q/))
#### Ex. 1
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -q "the" ./plos/*
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ echo $?
0
```
Here, grep doesn't print anything to the terminal but returns 0 to indicate that the word `"the"` is present in at least one file under `technical/plos`. This option might prove very useful in bash scripts where you want to stop if something isn't present - perhaps like running tests on data.
#### Ex. 2
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -q "antidisestablishmentarianism" ./government/About_LSC/*
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ echo $?
1
```
Grep now sets an exit code of 1 because it couldn't find the word `"antidisestablishmentarianism:` in any file under `technical/government/About_LSC`. In this manner grep can be useful as an existence checker, e.g. checking for passwords or tokens before pushing to GitHub.

### `grep -C`
Grep's C flag (confusingly uppercase this time) requests that the command provide a certain number of lines of context for each occurence of the target ([Baeldung](https://www.baeldung.com/linux/grep-show-surrounding-lines)).
#### Ex. 1
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -C 1 "frameshift" ./biomed/gb-2001-3-1-research0001.txt
          nucleotide deletion at position 105 of the homologous
          region results in a frameshift and the computer-deduced
          amino-acid sequence uses a start codon located 12
--
          NIP2;1 -like coding sequence for 35
          residues upstream of the position of the frameshift. A
          221 bp element showing 90% identity with fragments of the
--
          46, 47]. Also, polymorphisms in AQP genes, including
          frameshifts leading to truncated proteins, have been
          reported in different laboratory strains of
```
Grep searches for `"frameshift"` in the file  `technical/biomed/gb-2001-3-1-research0001.txt` and provides 1 line of context (above and below) for each occurence, dividing them with hyphens. This is very helpful for human-readable text where a reader might want to observe surrounding sentences to deduce meaning.
#### Ex. 2
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -C 2 "a state sponsored by terrorists" ./911report/*
./911report/chapter-6.txt-                the Taliban had proved unsuccessful. As one NSC staff note put it,
./911report/chapter-6.txt-            "Under the Taliban, Afghanistan is not so much a state sponsor of terrorism as it is
./911report/chapter-6.txt:                a state sponsored by terrorists." In early 2000, the
./911report/chapter-6.txt-                United States began a high-level effort to persuade Pakistan to use its influence
./911report/chapter-6.txt-                over the Taliban. In January 2000, Assistant Secretary of State Karl Inderfurth and
```
The command searches for the phrase `"a state sponsored by terrorists"` under the `technical/911report` directory and provides 2 lines of context above and below the target phrase. Beyond just text, this might be useful for pinpointing strings such as method calls in source code.

### `grep -n`
The n flag instructs grep to show line numbers for occurences ([PhoenixNAP](https://phoenixnap.com/kb/grep-command-linux-unix-examples#ftoc-heading-14)).
### Ex. 1
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -n "CAAX" ./plos/*
./plos/pmed.0020018.txt:33:        the amino acid sequence Cys-Ala-Ala-Xaa (“CAAX”) at the C-terminus of the Rho family of
```
Grep searches for the string `"CAAX"` under the `technical/plos` directory and shows that it is found at line 33. This line number could be fed to a tool like a text editor that allows jumping to a specific line.
#### Ex. 2
```
forkpoweroutlet@aaaaaaaaaa:~/docsearch/technical$ grep -n "GAO/AIMD" ./government/Gen_Account_Office/InternalControl_ai00021p.txt
9:GAO/AIMD-00-21.3.1
43:Page 1 GAO/AIMD-00-21.3.1 (11/99)
131:Page 5 GAO/AIMD-00-21.3.1 (11/99)
199:Page 8 GAO/AIMD-00-21.3.1 (11/99)
229:Page 9 GAO/AIMD-00-21.3.1 (11/99)
254:Page 10 GAO/AIMD-00-21.3.1 (11/99)
280:Page 11 GAO/AIMD-00-21.3.1 (11/99)
293:Page 12 GAO/AIMD-00-21.3.1 (11/99)
328:Page 13 GAO/AIMD-00-21.3.1 (11/99)
361:Page 14 GAO/AIMD-00-21.3.1 (11/99)
395:Page 15 GAO/AIMD-00-21.3.1 (11/99)
463:Page 17 GAO/AIMD-00-21.3.1 (11/99)
539:Page 20 GAO/AIMD-00-21.3.1 (11/99)
```
The command searches for `"GAO/AIMD"` in the `technical/government/Gen_Account_Office/InternalControl_ai00021p.txt` and provides the line numbers of occurences. In documents divided by a certain string, like above, this command can be especially useful when given to other programs that divide text.