<h1 align="center">
  <br />
  <img src="https://raw.githubusercontent.com/wiki/greymd/teip/img/logo.png" width="208" />
  <h4 align="center">Masking tape to help commands "do one thing well"</h4>
</h1>
<p align="center">
  <a href="https://github.com/greymd/teip/releases/latest"><img src="https://img.shields.io/github/release/greymd/teip.svg" alt="Latest version" /></a>
  <a href="https://crates.io/crates/teip" alt="crate.io"><img src="https://img.shields.io/crates/v/teip.svg"/></a>
  <a href="https://github.com/greymd/teip/actions?query=workflow%3ATest"><img src="https://github.com/greymd/teip/workflows/Test/badge.svg" alt="Test Status" /></a>
  <a href="LICENSE" alt="MIT License"><img src="http://img.shields.io/badge/license-MIT-blue.svg?style=flat" /></a>
</p>

## Taping

<p align="center">
  <img src="https://raw.githubusercontent.com/wiki/greymd/teip/img/teip_intro2.png" alt="Git Animation for Introduction" width="80%" />
</p>

* Convert timestamps in /var/log/secure to UNIX time

```bash
$ cat /var/log/secure | teip -c 1-15 -- date -f- +%s
```

* Replace 'WORLD' with 'EARTH' on lines containing 'HELLO'

```bash
$ cat file | teip -g HELLO -- sed 's/WORLD/EARTH/'
```

* Make characters upper case in the 2nd field of a CSV (RFC4180)

```bash
$ cat file.csv | teip --csv -f 2 -- tr a-z A-Z
```

* Edit the 2nd, 3rd and 4th fields of a TSV file

```bash
$ cat file.tsv | teip -D '\t' -f 2-4 -- tr a-z A-Z
```

* Edit lines containing 'hello' and the three lines before and after

```bash
$ cat access.log | teip -e 'grep -n -C 3 hello' -- sed 's/./@/g'
```

## Performance enhancement

`teip` allows a command to focus on its own task.

Here is a comparison of the processing time to replace approx 761,000 IP addresses with dummy ones in a 100 MiB text file.

<p align="center">
  <img src="https://raw.githubusercontent.com/wiki/greymd/teip/benchmark/secure_bench.svg" width="80%" alt="benchmark bar chart" />
</p>

See detail at <a href="https://github.com/greymd/teip/wiki/Benchmark">wiki > Benchmark</a>.

## Features

* Taping: Help the command "do one thing well"
  - Passing a partial range of the standard input to any command — whatever you want
  - The targeted command just actions the passed parts of the standard input
  - Flexible methods for selecting a range (Select like `awk`, `cut` or `grep`)

* High performance
  - The targeted command's standard input/output are written to and read from by multiple `teip` threads asynchronously.
  - If general UNIX commands in your environment can process a few-hundred MB file in a few seconds, then `teip` can do the same or better performance.

## Installation

### macOS (x86_64, ARM64) / Linux (x86_64)

Install [Homebrew](https://brew.sh/), and

```bash
brew install teip
```

### Linux (x86_64, ARM64)

#### `dpkg`

<!-- deb_url_start -->
```bash
wget https://github.com/greymd/teip/releases/download/v2.3.0/teip-2.3.0.$(uname -m)-unknown-linux-musl.deb
sudo dpkg -i ./teip*.deb
```
<!-- deb_url_end -->

#### `apt`

<!-- deb_url_start -->
```bash
wget https://github.com/greymd/teip/releases/download/v2.3.0/teip-2.3.0.$(uname -m)-unknown-linux-musl.deb
sudo apt install ./teip*.deb
```
<!-- deb_url_end -->

#### `dnf`

<!-- rpm_url_start -->
```bash
sudo dnf install https://github.com/greymd/teip/releases/download/v2.3.0/teip-2.3.0.$(uname -m)-unknown-linux-musl.rpm
```
<!-- rpm_url_end -->

#### `yum`

<!-- rpm_url_start -->
```bash
sudo yum install https://github.com/greymd/teip/releases/download/v2.3.0/teip-2.3.0.$(uname -m)-unknown-linux-musl.rpm
```
<!-- rpm_url_end -->

<!-- release_url_start -->
If necessary, check the hash value from the [latest release page](https://github.com/greymd/teip/releases/tag/v2.3.0).
Files whose filenames end with `sha256` have hash values listed.
<!-- release_url_end -->


### Windows (x86_64)

<!-- ins_url_start -->
Download installer from [here](https://github.com/greymd/teip/releases/download/v2.3.0/teip_installer-2.3.0-x86_64-pc-windows-msvc.exe).
<!-- ins_url_end -->

See [Wiki > Use on Windows](https://github.com/greymd/teip/wiki/Use-on-Windows) for detail.

### Other architectures

<!-- release_url_start -->
Check the [latest release page](https://github.com/greymd/teip/releases/tag/v2.3.0) for executables for the platform you are using.
<!-- release_url_end -->

If not present, please build teip from source.

### Build from source

With Rust's package manager cargo

```
cargo install teip
```

To enable Oniguruma regular expression (`-G` option), build with `--features oniguruma` option.
Please make sure the `libclang` shared library is available in your environment.

```bash
### Ubuntu
$ sudo apt install cargo clang
$ cargo install teip --features oniguruma
```

```bash
### Red Hat base OS
$ sudo dnf install cargo clang
$ cargo install teip --features oniguruma
```

```powershell
### Windows (PowerShell) and choco (chocolatey.org)
PS C:\> choco install llvm
PS C:\> cargo install teip --features oniguruma
```

## Usage

```
USAGE:
  teip -g <pattern> [-Gosvz] [--] [<command>...]
  teip -c <list> [-svz] [--] [<command>...]
  teip -l <list> [-svz] [--] [<command>...]
  teip -f <list> [-d <delimiter> | -D <pattern> | --csv] [-svz] [--] [<command>...]
  teip -e <string> [-svz] [--] [<command>...]

OPTIONS:
    -g <pattern>        Act on lines that match the regular expression <pattern>.
        -o              -g acts on only matched ranges.
        -G              -g interprets Oniguruma regular expressions.
    -c <list>           Act on these characters.
    -l <list>           Act on these lines.
    -f <list>           Act on these white-space separated fields.
        -d <delimiter>  Use <delimiter> for the field delimiter of -f.
        -D <pattern>    Use regular expression <pattern> for the field delimiter of -f
        --csv           -f interprets <list> as field number of a CSV according to
                        RFC 4180, instead of whitespace separated fields.
    -e <string>         Execute <string> in another process that will receive identical
                        standard input as the main teip command, emitting numbers to be
                        used as line numbers for actioning.

FLAGS:
    -h, --help          Prints help information.
    -V, --version       Prints version information.
    -s                  Execute a new command for each actioned chunk.
        --chomp         The command spawned by -s receives the standard input without
                        trailing newlines.
    -I  <replace-str>   Replace the <replace-str> with the actioned chunk in <command>,
                        implying -s.
    -v                  Invert the range of actioning.
    -z                  Line delimiter is NUL instead of a newline.

ALIASES:
    -g <pattern>
        -A <number>     Alias of -e 'grep -n -A <number> <pattern>'
        -B <number>     Alias of -e 'grep -n -B <number> <pattern>'
        -C <number>     Alias of -e 'grep -n -C <number> <pattern>'
    --sed <pattern>     Alias of -e 'sed -n "<pattern>="'
    --awk <pattern>     Alias of -e 'awk "<pattern>{print NR}"'
```

## Getting Started

Try this at first:

```bash
$ echo "100 200 300 400" | teip -f 3
```

The result is almost the same as the input, but "300" is highlighted and surrounded by `[...]`,
because `-f 3` specifies the 3rd field of space-separated input.

```bash
100 200 [300] 400
```

Understand that the area enclosed in `[...]` is a **hole** in the masking tape.

<img src="https://raw.githubusercontent.com/wiki/greymd/teip/img/teip_hole.png" width="300" />

Next, put the `sed` and its arguments at the end.

```bash
$ echo "100 200 300 400" | teip -f 3 sed 's/./@/g'
```

The result is as below.
The highlight and `[...]` will not be present when a command is added.

```
100 200 @@@ 400
```

As you can see, the `sed` command only acted on the input defined by the "hole" and ignored the masked
parts.  Technically, `teip` passes only the highlighted part to the `sed` process, and replaces the
highlighted part with the result of the `sed` command.

Of course, any command you like can be specified.
It is called the **targeted command** in this article.

Let's try `cut` as the targeted command, to extract the first character only.

```bash
$ echo "100 200 300 400" | teip -f 3 cut -c 1
teip: Invalid arguments.
```

Oops! Why did this fail?

This is because the `cut`command  uses the `-c` option.
An option of the same name is also provided by `teip`, which is confusing.

When specifying a targeted command to `teip`, it is better to give it after `--`.
Then, `teip` interprets any arguments after `--` as the targeted command and its arguments.

```bash
$ echo "100 200 300 400" | teip -f 3 -- cut -c 1
100 200 3 400
```

Great — the first character `3` is extracted from `300`!

Although `--` is not always necessary, it is always better to use it.
So, `--` is used in all the examples from here on.

Now let's double these number with `awk`.
The command looks like the following (Note that the variable to be doubled is not `$3`).

```bash
$ echo "100 200 300 400" | teip -f 3 -- awk '{print $1*2}'
100 200 600 400
```

OK, the selection in the "hole" went from 300 to 600.

Now, let's change `-f 3` to `-f 3,4` and run teip.

```bash
$ echo "100 200 300 400" | teip -f 3,4 -- awk '{print $1*2}'
100 200 600 800
```

The numbers in the 3rd and 4th fields were doubled!

As you may have noticed, the argument to `-f` is compatible with the __LIST__ of `cut`.
You can refer to `cut --help` to see how it works.

Examples:

```bash
$ echo "100 200 300 400" | teip -f -3 -- sed 's/./@/g'
@@@ @@@ @@@ 400

$ echo "100 200 300 400" | teip -f 2-4 -- sed 's/./@/g'
100 @@@ @@@ @@@

$ echo "100 200 300 400" | teip -f 1- -- sed 's/./@/g'
@@@ @@@ @@@ @@@
```

## Select range by character

The `-c` option allows you to specify a range by character.
The below example is specifying the 1st, 3rd, 5th, 7th characters and applying the `sed` command to them.

```bash
$ echo ABCDEFG | teip -c 1,3,5,7
[A]B[C]D[E]F[G]

$ echo ABCDEFG | teip -c 1,3,5,7 -- sed 's/./@/'
@B@D@F@
```

Like `-f`, the argument to `-c` is compatible with `cut`'s __LIST__.

## Processing delimited text like CSV and TSV

The `-f` option recognizes delimited fields [like `awk`](https://www.gnu.org/software/gawk/manual/html_node/Regexp-Field-Splitting.html) by default.

Any continuous whitespace (all forms of whitespace categorized by [Unicode](https://www.unicode.org/Public/UCD/latest/ucd/PropList.txt)) is interpreted as a single delimiter.

```bash
$ printf "A       B \t\t\t\   C \t D" | teip -f 3 -- sed s/./@@@@/
A       B                       @@@@   C         D
```

This behavior might be inconvenient for the processing of CSV and TSV.

However, the `-d` option in conjunction with `-f` can be used to specify a delimiter.
You can process a simple CSV file like this:

```bash
$ echo "100,200,300,400" | teip -f 3 -d , -- sed 's/./@/g'
100,200,@@@,400
```

In order to process TSV, the TAB character must be given at the command line.
If you are using Bash, type `$'\t'` which is in the form of [ANSI-C Quoting](https://www.gnu.org/software/bash/manual/html_node/ANSI_002dC-Quoting.html).

```bash
$ printf "100\t200\t300\t400\n" | teip -f 3 -d $'\t' -- sed 's/./@/g'
100     200     @@@     400
```

`teip` also provides `-D` option to specify an extended regular expression as the delimiter.
This is useful when you want to ignore consecutive delimiters, or when there are multiple types of delimiter.

```bash
$ echo 'A,,,,,B,,,,C' | teip -f 2 -D ',+'
A,,,,,[B],,,,C
```

```bash
$ echo "1970-01-02 03:04:05" | teip -f 2-5 -D '[-: ]'
1970-[01]-[02] [03]:[04]:05
```

The TAB character regular expression (`\t`) can also be specified with the `-D` option.

```
$ printf "100\t200\t300\t400\n" | teip -f 3 -D '\t' -- sed 's/./@/g'
100     200     @@@     400
```

For the available regular expression notations, refer to [regular expression of Rust](https://docs.rs/regex/1.3.7/regex/).

## Complex CSV processing

If you want to process a complex CSV file, such as the one below, which has columns surrounded by double quotes, use the `-f` option together with the `--csv` option.

```csv
Name,Address,zipcode
Sola Harewatar,"Doreami Road 123
Sorashido city",12877
Yui Nagomi,"Nagomi Street 456, Nagomitei, Oishina town",26930-0312
"Conectol Motimotit Hooklala Glycogen Comex II a.k.a ""Kome kome""","Cooking dam",513123
```

With `--csv`, teip will parse the input as a CSV file according to [RFC4180](https://www.rfc-editor.org/rfc/rfc4180). Thus, you can use `-f` to specify column numbers for CSV files with complex structures.

For example, the CSV above will have a "hole" as shown below.

```
$ cat tests/sample.csv | teip --csv -f2 
Name,[Address],zipcode
Sola Harewatar,["Doreami Road 123]
[Sorashido city"],12877
Yui Nagomi,["Nagomi Street 456, Nagomitei, Oishina town"],26930-0312
"Conectol Motimotit Hooklala Glycogen Comex II a.k.a ""Kome kome""",["Cooking dam"],513123
```

Because `-f2` was specified, there is a hole in the second column of each row.
The following command is an example of rewriting all characters in the second column to "@".

```
$ cat tests/sample.csv  | teip --csv -f2 -- sed 's/[^"]/@/g'
Name,@@@@@@@,zipcode
Sola Harewatar,"@@@@@@@@@@@@@@@@
@@@@@@@@@@@@@@",12877
Yui Nagomi,"@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@",26930-0312
"Conectol Motimotit Hooklala Glycogen Comex II a.k.a ""Kome kome""","@@@@@@@@@@@",513123
```

Notes for the `--csv` option:

* Double quotes `"` surrounding fields are also included in the holes.
* Escaped double quotes `""` are treated as-is; two double quotes `""` are given as input to the targeted command.
* Fields containing newlines will have multiple holes, separated by newlines, instead of a single hole.
  * However, if the `-s` or `-z` option is used, such a field is treated as a single hole, and line breaks are included.

## Matching with Regular Expressions

You can also use `-g` to select a specific line matching a regular expression as the hole location.

```bash
$ echo -e "ABC1\nEFG2\nHIJ3" | teip -g '[GJ]\d'
ABC1
[EFG2]
[HIJ3]
```

By default, the entire line containing the pattern is the range of holes.
With the -o option, the range of the holes will ony cover the matched range.

```bash
$ echo -e "ABC1\nEFG2\nHIJ3" | teip -og '[GJ]\d'
ABC1
EF[G2]
HI[J3]
```

Note that `-og` is one of the most useful idioms and is frequently used in this manual.

Here is an example using `\d`, which matches numbers.

```bash
$ echo ABC100EFG200 | teip -og '\d+'
ABC[100]EFG[200]

$ echo ABC100EFG200 | teip -og '\d+' -- sed 's/.*/@@@/g'
ABC@@@EFG@@@
```

This feature is quite versatile and can be useful for handling files that have no fixed form such as logs, markdown, etc.

## What commands are appropriate?

`teip` passes the strings from the hole line-by-line, so that each hole is one line of input.
Therefore, a targeted command must follow the below rule.

* **A targeted command must print a single line of result for each line of input.**

In the simplest example, the `cat` command always succeeds,
because the `cat` prints the same number of lines as it is given in input.

```bash
$ echo ABCDEF | teip -og . -- cat
ABCDEF
```

If the above rule is not satisfied, the result will be inconsistent.
For example, `grep` may fail.
Here is an example.

```bash
$ echo ABCDEF | teip -og .
[A][B][C][D][E][F]

$ echo ABCDEF | teip -og . -- grep '[ABC]'
ABC
teip: Output of given command is exhausted

$ echo $?
1
```

`teip` did not receive results corresponding to the holes of D, E, and F.
That is why the above example fails.

If an inconsistency occurs, `teip` will exit with the error message.
Also, the exit status will be 1.

To learn more about `teip`'s behavior, see [Wiki > Chunking](https://github.com/greymd/teip/wiki/Chunking).

## Advanced usage

### Solid mode (`-s`)

If you want to use a command that does not satisfy the condition, **"A targeted command must print a single line of result for each line of input"**, enable "Solid mode" with the `-s` option.

Solid mode spawns the targeted command multiple times: once for each hole in the input.

```bash
$ echo ABCDEF | teip -s -og . -- grep '[ABC]'
```

In the above example, understand that the following commands are executed by `teip` internally:

```bash
$ echo A | grep '[ABC]' # => A
$ echo B | grep '[ABC]' # => B
$ echo C | grep '[ABC]' # => C
$ echo D | grep '[ABC]' # => Empty
$ echo E | grep '[ABC]' # => Empty
$ echo F | grep '[ABC]' # => Empty
```

The empty result is replaced with an empty string.
Therefore, D, E, and F are replaced with the empty string.

```bash
$ echo ABCDEF | teip -s -og . -- grep '[ABC]'
ABC

$ echo $?
0
```

However, this option is not suitable for processing large files because of its high overhead, which can significantly degrade performance.

#### Solid mode with placeholder (`-I <replace-str>`)

If you want to use the contents of the hole as an argument to the targeted command, use the `-I` option.

```bash
$ echo AAA BBB CCC | teip -f 2 -I @ -- echo '[@]'
AAA [BBB] CCC
```

`<replace-str>` can be any string, and multiple characters are allowed.

```bash
$ seq 5 | teip -f 1 -I NUMBER -- awk 'BEGIN{print NUMBER * 3}'
3
6
9
12
15
```

Please note that `-s` is automatically enabled with `-I`.
Therefore, it is not suitable for processing huge files.
In addition, the targeted command does not get any input from stdin.
The targeted command is expected to work without stdin.

#### Solid mode with `--chomp`

If the `-s` option does not work as expected, `--chomp` may be helpful.

A targeted command in solid mode always accepts input with a newline (`\x0A`) at the end.
This is because `teip` assumes the use of commands which return a single line of output in response to a single line of input.
Therefore, even if there is no line break in the hole, a line break is added, to ensure it is treated as a single line of input.

However, there are situations where this behavior is inconvenient.
For example, when using commands whose behavior changes depending on the presence or absence of a newline.

```bash
$ echo AAABBBCCC | teip -og BBB -s
AAA[BBB]CCC
$ echo AAABBBCCC | teip -og BBB -s -- tr '\n' '@'
AAABBB@CCC
```

The above is an example where the targeted command is: "`tr` command which converts newlines (`\x0A`) to @".
"BBB" does not contain a newline, but the output is "BBB@", because implicitly-added line breaks have been processed.
To prevent this behavior, use the `--chomp` option.
This option gives the targeted command pure input with no newlines added.

```bash
$ echo AAABBBCCC | teip -og BBB -s --chomp -- tr '\n' '@'
AAABBBCCC
```

`--chomp` is useful whenever using commands which interpret and process input as binary such as `tr`.
Below is an example of "removing newlines from the second column of a CSV which contains newlines.

```bash
$ cat tests/sample.csv
Name,Address,zipcode
Sola Harewatar,"Doreami Road 123
Sorashido city",12877
```

The result is:

```bash
$ cat tests/sample.csv | teip --csv -f 2 -s --chomp -- tr '\n' '@'
Name,Address,zipcode
Sola Harewatar,"Doreami Road 123@Sorashido city",12877
```

### Line number (`-l`)

You can specify a line number and drill holes only in that line.

```bash
$ echo -e "ABC\nDEF\nGHI" | teip -l 2
ABC
[DEF]
GHI
```

```bash
$ echo -e "ABC\nDEF\nGHI" | teip -l 1,3
[ABC]
DEF
[GHI]
```

### Overlay `teip`s

Any command can be used with `teip`, surprisingly, even if it is **`teip` itself**.

```bash
$ echo "AAA@@@@@AAA@@@@@AAA" | teip -og '@.*@'
AAA[@@@@@AAA@@@@@]AAA

$ echo "AAA@@@@@AAA@@@@@AAA" | teip -og '@.*@' -- teip -og 'A+'
AAA@@@@@[AAA]@@@@@AAA

$ echo "AAA@@@@@AAA@@@@@AAA" | teip -og '@.*@' -- teip -og 'A+' -- tr A _
AAA@@@@@___@@@@@AAA
```

In other words, by composing multiple functions of `teip` with AND conditions, it is possible to drill holes in a more complex range.
Furthermore, this works asynchronously and in multi-processes, similar to the shell pipeline.
Performance will hardly degrade unless the machine reaches the limits of parallelism.

### Oniguruma regular expression (`-G`)

If `-G` option is given together with `-g`, the regular expressin is interpreted as an [Oniguruma regular expression](https://github.com/kkos/oniguruma/blob/master/doc/RE).
For example, "keep" and "look-ahead" syntax can be used.

```bash
$ echo 'ABC123DEF456' | teip -G -og 'DEF\K\d+'
ABC123DEF[456]

$ echo 'ABC123DEF456' | teip -G -og '\d+(?=D)'
ABC[123]DEF456
```

### Empty holes

If a blank field exists when the `-f` option is used, the blank is not ignored and is treated as an empty hole.

```bash
$ echo ',,,' | teip -d , -f 1-
[],[],[],[]
```

Therefore, the following command can work (Note that `.*` matches empty values as well).

```bash
$ echo ',,,' | teip -f 1- -d, sed 's/.*/@@@/'
@@@,@@@,@@@,@@@
```

In the above example, the `sed` command reads four newline characters and prints `@@@` four times.

### Invert match (`-v`)

The `-v` option allows you to invert the range of holes.
When the `-f` or `-c` option is used with `-v`, holes are made in the complement of the specified field.

```bash
$ echo 1 2 3 4 5 | teip -v -f 1,3,5 -- sed 's/./_/'
1 _ 3 _ 5
```

Of course, `-v` can also be used with `-og`.

```bash
$ printf 'AAA\n123\nBBB\n' | teip -vg '\d+' -- sed 's/./@/g'
@@@
123
@@@
```

### Zero-terminated mode (`-z`)

If you want to process the data in a more flexible way, the `-z` option may be useful.
This option allows you to use the NUL character (the ASCII NUL character) as a line delimiter, instead of the newline character.
It behaves like `-z` provided by GNU sed or GNU grep, or the `-0` option provided by xargs.

```bash
$ printf '111,\n222,33\n3\0\n444,55\n5,666\n' | teip -z -f3 -d,
111,
222,[33
3]
444,55
5,[666]
```

With this option, the standard input is interpreted per each NUL character rather than per each newline character.
You should also pay attention to the fact that strings in the hole have the NUL character appended instead of a newline character.

If you use a targeted command that cannot handle NUL characters (and cannot print NUL-separated results), the final result can be unintended.

```bash
$ printf '111,\n222,33\n3\0\n444,55\n5,666\n' | teip -z -f3 -d, -- sed -z 's/.*/@@@/g'
111,
222,@@@
444,55
5,@@@

$ printf '111,\n222,33\n3\0\n444,55\n5,666\n' | teip -z -f3 -d, -- sed 's/.*/@@@/g'
111,
222,@@@
@@@
444,55
5,teip: Output of given command is exhausted
```

This option is useful for treating multiple lines as a single combined input.

```bash
$ cat test.html | teip -z -og '<body>.*</body>'
<html>
<head>
  <title>AAA</title>
</head>
[<body>
  <div>AAA</div>
  <div>BBB</div>
  <div>CCC</div>
</body>]
</html>

$ cat test.html | teip -z -og '<body>.*</body>' -- grep -a BBB
<html>
<head>
  <title>AAA</title>
</head>
  <div>BBB</div>
</html>
```

### External execution for match offloading (`-e`)

`-e` is the option to use external commands to define pattern matching.
Without `-e`, you must use `teip`'s own functions, such as `-c` or `-g`, to control the position of the holes on the masking tape.
With `-e`, however, you can use the external commands you are familiar with to specify the range of holes.

`-e` allows you to specify a shell pipeline as a string.
On a UNIX-like OS, this pipeline is executed via `/bin/sh`; on Windows via `cmd.exe`.

For example, given a simple pipeline `echo 3`, which outputs `3`, only the third line will be actioned by teip.

```bash
$ echo -e 'AAA\nBBB\nCCC' | teip -e 'echo 3'
AAA
BBB
[CCC]
```

This works even if the `-e` output is somewhat "dirty".
For example, if spaces or tab characters are included at the beginning of the `-e` output, they are ignored.
Also, once a number is seen, all non-numerical characters to the right of the number are ignored.

```bash
$ echo -e 'AAA\nBBB\nCCC' | teip -e 'echo " 3"'
AAA
BBB
[CCC]
$ echo -e 'AAA\nBBB\nCCC' | teip -e 'echo " 3:testtest"'
AAA
BBB
[CCC]
```

Technically, the first captured group in the regular expression `^\s*([0-9]+)` is interpreted as a line number.

`-e` will also recognize multiple numbers if the pipeline provides multiple lines of numbers.
For example, the `seq` command to display only odd numbers up to 10 is

```bash
$ seq 1 2 10
1
3
5
7
9
```

This means that only odd-numbered rows can be actioned by specifying the following:

```bash
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | teip -e 'seq 1 2 10' -- sed 's/. /@/g'
@@@
BBB
@@@
DDD
@@@
FFF
```

Note that the order of the numbers must be ascending.
Now, on its own, this looks like a feature that is just a slight improvement of the `-l` option.

However, the breakthrough feature of `-e` is that **the pipeline obtains identical standard input as the main `teip` command**.
Thus, it generates output using not only `seq` and `echo`, but also commands such as `grep`, `sed`, and `awk`, which process the standard input.

Let's look at a more concrete example.
The following is a `grep` command that prints **the line numbers of the line containing the string "CCC" and the two lines after it**.

```bash
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | grep -n -A 2 CCC
3:CCC
4-DDD
5-EEE
```

If you give this command to `-e`, you can punch holes in **the line containing the string "CCC" and the two lines after it**!

```bash
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | teip -e 'grep -n -A 2 CCC'
AAA
BBB
[CCC]
[DDD]
[EEE]
FFF
```

`grep` is not the only useful command for `-e`.
GNU `sed` has `=`, which prints the line number being processed.
Below is an example of how to use `=` to drill from the line containing "BBB" to the line containing "EEE".

```bash
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | teip -e 'sed -n "/BBB/,/EEE/="'
AAA
[BBB]
[CCC]
[DDD]
[EEE]
FFF
```

Of course, similar operations can also be done with `awk`.

```bash
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | teip -e 'awk "/BBB/,/EEE/{print NR}"'
```

The following is an example of combining the commands `nl` and `tail`.
You can make holes in only the last three lines of input!

```bash
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | teip -e 'nl -ba | tail -n 3'
AAA
BBB
CCC
[DDD]
[EEE]
[FFF]
```

The argument to `-e` is a single string.
The pipe (`|`) and other symbols can be used within it.

### Alias options (`-A`, `-B`, `-C`, `--awk`, `--sed`)

There are several **experimental options** which are aliases of `-e` and specific directives.
These options may be discontinued in the future since they are only experimental.
Do not use them in a script or something that is not a one-off.

#### `-A <number>`

This is an alias of `-e 'grep -n -A <number> <pattern>'`.
If it is used together with `-g <pattern>`, it makes holes in rows matching `<pattern>`, and `<number>` rows after the match.

```bash
$ cat AtoG.txt | teip -g B -A 2
A
[B]
[C]
[D]
E
F
G
```


#### `-B <number>`

This is an alias of `-e 'grep -n -B <number> <pattern>'`
If it is used together with `-g <pattern>`, it makes holes in rows matching `<pattern>`, and `<number>` rows before the match.

```
$ cat AtoG.txt | teip -g E -B 2
A
B
[C]
[D]
[E]
F
G
```



#### `-C <number>`

This is an alias of `-e 'grep -n -C <number> <pattern>'`.
If it is used together with `-g <pattern>`, it makes holes in rows matching `<pattern>`, and `<number>` rows before and after the match.

```bash
$ cat AtoG.txt | teip -g E -C 2
A
B
[C]
[D]
[E]
[F]
[G]
```

#### `--sed <pattern>`

This is an alias of `-e 'sed -n "<pattern>="`.

```bash
$ cat AtoG.txt | teip --sed '/B/,/E/'
A
[B]
[C]
[D]
[E]
F
G
```

```bash
$ cat AtoG.txt | teip --sed '1~3'
[A]
B
C
[D]
E
F
[G]
```

#### `--awk <pattern>`

This is an alias of `-e 'awk "<pattern>{print NR}"`.

```bash
$ cat AtoG.txt | teip --awk '/B/,/E/'
A
[B]
[C]
[D]
[E]
F
G
```

```bash
$ cat AtoG.txt | teip --awk 'NR%3==0'
A
B
[C]
D
E
[F]
G
```


## Environment variables

`teip` refers to the following environment variables.
Add a statement to your default shell's startup file (i.e `.bashrc`, `.zshrc`) to change them as you like.

### `TEIP_HIGHLIGHT`

**DEFAULT VALUE:** `\x1b[36m[\x1b[0m\x1b[01;31m{}\x1b[0m\x1b[36m]\x1b[0m`

The default format for highlighting holes.
It must include at least one `{}` as a placeholder.

Example:

```bash
$ export TEIP_HIGHLIGHT="<<<{}>>>"
$ echo ABAB | teip -og A
<<<A>>>B<<<A>>>B

$ export TEIP_HIGHLIGHT=$'\x1b[01;31m{}\x1b[0m'
$ echo ABAB | teip -og A
ABAB  ### Same color as grep
```

[ANSI Escape Sequences](https://gist.github.com/fnky/458719343aabd01cfb17a3a4f7296797) and [ANSI-C Quoting](https://www.gnu.org/software/bash/manual/html_node/ANSI_002dC-Quoting.html) are helpful for customizing this value.

### `TEIP_GREP_PATH`

**DEFAULT VALUE:** `grep`

The path to the `grep` command used by the `-A`, `-B`, and `-C` options.
For example, if you want to use `ggrep` instead of `grep`, set this variable to `ggrep`.

```bash
$ export TEIP_GREP_PATH=/opt/homebrew/bin/ggrep
$ echo -e 'AAA\nBBB\nCCC\nDDD\nEEE\nFFF' | teip -g CCC -A 2
AAA
BBB
[CCC]
[DDD]
[EEE]
FFF
```

### `TEIP_SED_PATH`

**DEFAULT VALUE:** `sed`

The path to the `sed` command used by the `--sed` option.
For example, if you want to use `gsed` instead of `sed`, set this variable to `gsed`.

### `TEIP_AWK_PATH`

**DEFAULT VALUE:** `awk`

The path to the `awk` command used by the `--awk` option.
For example, if you want to use `gawk` instead of `awk`, set this variable to `gawk`.

## Background

### Why make this?

See this [post](https://dev.to/greymd/teip-masking-tape-for-shell-is-what-we-needed-5e05).

### Why "teip"?

* [tee](https://en.wikipedia.org/wiki/Tee_%28command%29) + in-place.
* And it sounds similar to Masking-"tape".

## License

### Modules imported/referenced from other repositories

Thank you so much for these helpful modules!

* ./src/list/ranges.rs
  - One of the modules used in the `cut` command is from [uutils/coreutils](https://github.com/uutils/coreutils)
  - The original source code is distributed under the MIT license
  - The license file is in the same directory

* ./src/csv/parser.rs
  - Many parts of the source code are referenced from [BurntSushi/rust-csv](https://github.com/BurntSushi/rust-csv).
  - The original source code is dual-licensed under the MIT and Unlicense

### Source code

Teip is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

### Logo

<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />The logo of teip is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.
