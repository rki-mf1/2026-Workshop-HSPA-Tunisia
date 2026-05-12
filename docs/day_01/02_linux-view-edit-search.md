[⬅ Back to main page](../README.md)

# Viewing, Editing, Compressing, and Searching Files

## 🎯 Learning goals

By the end of this lesson, you should be able to:

- unpack and inspect `.tar.gz` archives
- work with compressed `.gz` files using `gzip`, `gunzip`, `zcat`, and `zless`
- search text with `grep`
- combine commands using pipes (`|`)
- extract columns from tabular files
- write and run Bash scripts

## Before you start

Open a terminal and move into the workshop repository:

```bash
cd ~/2026-Workshop-HSPA-Tunisia
```

Create a safe practice area for this session:

```bash
mkdir -p scratch
cd scratch
```

Check where you are:

```bash
pwd
```

---

## 1. Working with `.tar.gz` archives

![tar.gz](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkgOCjTC1nwiLFwYZjQ7IZzfxKGlMoU_AnHkawHGDeasDLthqgdNmQbE4lLePalNpEM12ooVLOWKPfY4IQ0F8UUkhAr8fkRVt-sO4R6KmDI-pM_ulAMIksmg71AVXcENnUNDKy-kUOocQ/s1600/tar-gzip.jpg)

*What is a `.tar.gz` file?*

In bioinformatics we often work with large datasets and collections of many related files. To make them easier to share and to reduce their size, these files are frequently packaged into `.tar.gz` archives.

- `.tar` → stands for [tape archive](https://en.wikipedia.org/wiki/Tar_(computing)). It combines multiple files and folders into a single archive (like putting them into a box), but does not compress them.

- `.gz` → stands for [gzip compression](https://en.wikipedia.org/wiki/Gzip). It shrinks the size of the file using compression.

When combined, `.tar.gz` means:
- Multiple files and/or directories are bundled together into one archive.
- That archive is then compressed to save space.


We prepared few of them in `data/tutorial_data`. First, copy them in your current working directory:

```bash
cp ~/2026-Workshop-HSPA-Tunisia/data/tutorial_data/*.tar.gz .
```

You can **extract** `.tar.gz` like this:

```bash
tar -xvzf references.tar.gz
tar -xvzf SRR32055875_irma_output.tar.gz
```

### Meaning of the options

- `-x` — extract
- `-v` — [verbose](https://en.wikipedia.org/wiki/Verbose_mode) output
- `-z` — archive is gzip-compressed
- `-f` — file name follows

Check what was extracted:

```bash
ls -lh
```

If `tree` is installed, view the directory structure:

```bash
tree
```

If `tree` is not available, use:

```bash
ls -R
```

Compare the sizes of compressed file vs uncompressed directory:

```bash
du -hs SRR32055875_irma_output.tar.gz
du -hs SRR32055875_irma_output
```

Try making your first `.tar.gz`:

```bash
tar -cvzf my_first_tar_file.tar.gz references
```

### 💬 Discussion

- Why are `.tar.gz` archives useful in bioinformatics?
- What is the difference between compressing and de-compressing `tar.gz` files?

---

## 2. Compression with `gzip`

Genomics files are often very large, frequently reaching several gigabytes. To reduce storage requirements, they are commonly compressed with `gzip`. Although these files contain binary data and cannot be read directly with standard text tools, you can still inspect them from the command line without decompressing them first.

First, let’s try seeing zipped file using `head` command.

```bash
head references/sc2/*.fasta.gz
```

🎉 Congratulations! Instead of a readable FASTA file, you have discovered mysterious alien 👽 symbols — clear evidence that SARS-CoV-2 may have arrived from a galaxy far, far away.

What actually happened is that head is showing you the raw compressed binary data of the `.gz` file — which looks like nonsense. To properly look inside compressed FASTA files, you need to use tools that understand `gzip` compression, such as:

```bash
zcat references/sc2/*.fasta.gz | head
```

Let's copy it in current working directory, and then uncompress it:

```bash
cp references/sc2/*.fasta.gz . 

# Uncompress but keep compressed file to compare sizes
gunzip -k Wuhan-Hu-1_ASM985889v3.fasta.gz
ls -lh

# Remove gz file
rm Wuhan-Hu-1_ASM985889v3.fasta.gz
ls -lh 
```

To compress file:

```bash
gzip Wuhan-Hu-1_ASM985889v3.fasta
ls -lh
```

### 💬 Discussion

- What changed in the file size?
- Why is compression especially common for FASTA and FASTQ files?
- What is the difference between `gzip` and `gunzip`?

---

## 3. Inspect compressed files without unpacking them

Try and compare these commands:

```bash
cat Wuhan-Hu-1_ASM985889v3.fasta.gz
```

```bash
zcat Wuhan-Hu-1_ASM985889v3.fasta.gz
```

```bash
zcat Wuhan-Hu-1_ASM985889v3.fasta.gz | head
```

```bash
zcat Wuhan-Hu-1_ASM985889v3.fasta.gz | tail
```

```bash
zless Wuhan-Hu-1_ASM985889v3.fasta.gz
```

### 💬 Discussion

- Why is `cat` not useful on a `.gz` file?
- When is `zless` better than unzipping a large file first?

---

## 4. Search text with `grep`

Let's copy one of the VCF files in our current working directory to explore it:

```bash
cp SRR32055875_irma_output/A_HA_H3.vcf .
```

Search for one word:

```bash
grep "PASS" A_HA_H3.vcf
grep "FILTER" A_HA_H3.vcf
```

Count matching lines:

```bash
grep -c "PASS" A_HA_H3.vcf
```

Search case-insensitively:

```bash
grep "pAsS" A_HA_H3.vcf
grep -i "pAsS" A_HA_H3.vcf
```

Search for lines that start with a pattern:

```bash
zgrep "^>" Wuhan-Hu-1_ASM985889v3.fasta
grep "^#" A_HA_H3.vcf
```

### 💬 Discussion

- What does `-c` do?
- What does `-i` do?
- What does `^>` and `^#` mean?

---

## 5. Pipes

A pipe sends the output of one command into the next command.

Examples:

```bash
cat A_HA_H3.vcf | wc -l
```

```bash
grep "PASS" A_HA_H3.vcf | wc -l
```

```bash
grep "PASS" A_HA_H3.vcf | less
```

```bash
grep "PASS" A_HA_H3.vcf | head
```

### 💬 Discussion

- Why is `|` useful?
- Which part runs first in `grep "PASS" A_HA_H3.vcf | less`?

---

## 6. Work with tabular data from the command line

Create a small CSV file:

```bash
echo "Species,Serogroup,Continent" > data.csv
echo "Vibrio cholerae,O1,Essos" >> data.csv
echo "Vibrio cholerae,O139,Westeros" >> data.csv
echo "Vibrio cholerae,O139,Essos" >> data.csv
```

Display it:

```bash
cat data.csv
```

### Extract columns with `cut`

Get the `Serogroup` and `Continent` columns:

```bash
cut -d "," -f 2-3 data.csv
```

Get only countries for serogroup `O139`:

```bash
grep "O139" data.csv | cut -d "," -f 3
```

### Sort values with `sort`

```bash
cut -d "," -f 3 data.csv | tail -n +2 | sort
```

### Find unique values with `uniq`

```bash
cut -d "," -f 3 data.csv | tail -n +2 | sort | uniq
```

Count unique values:

```bash
cut -d "," -f 3 data.csv | tail -n +2 | sort | uniq -c
```

Save unique serogroups to a new file:

```bash
cut -d "," -f 2 data.csv | tail -n +2 | sort | uniq > unique_serogroup.txt
```

### Count lines with `wc`

```bash
wc data.csv
wc -l data.csv
wc -w data.csv
```

### 💬 Discussion

- Why is `sort` usually needed before `uniq`?
- What does `tail -n +2` do in the examples above?

---

## 7. Write your first Bash script

Create a directory for scripts:

```bash
cd ~/2026-Workshop-HSPA-Tunisia/scratch
mkdir -p scripts
cd scripts
```

Create a small script called `create_project.sh`:

```bash
nano create_project.sh
```

Paste this content:

```bash
#!/bin/bash

TARGET_DIR=$1
PROJECT_NAME=$2

mkdir -p "$TARGET_DIR/$PROJECT_NAME"
mkdir -p "$TARGET_DIR/$PROJECT_NAME/data"
mkdir -p "$TARGET_DIR/$PROJECT_NAME/data/raw_data"
mkdir -p "$TARGET_DIR/$PROJECT_NAME/data/references"
mkdir -p "$TARGET_DIR/$PROJECT_NAME/analyses"
mkdir -p "$TARGET_DIR/$PROJECT_NAME/scripts"
mkdir -p "$TARGET_DIR/$PROJECT_NAME/docs"
```

Save and exit.

### What this script introduces

- `#!/bin/bash` — the shebang; tells the system to use Bash
- `$1` — first argument
- `$2` — second argument
- `mkdir -p` — create directories, including parent directories if needed

---

## 8. Make the script executable and run it

Give the script execute permission:

```bash
ls -lh
chmod +x create_project.sh
ls -lh
```

Run it explicitly with Bash:

```bash
bash create_project.sh ~/2026-Workshop-HSPA-Tunisia/scratch/scripts demo_project
```

Run it directly:

```bash
./create_project.sh ~/2026-Workshop-HSPA-Tunisia/scratch/scripts demo_project_2

# or

./create_project.sh . demo_project_3
```

Check the results:

```bash
tree demo_project
```

If `tree` is not installed:

```bash
ls -R demo_project
```

### 💬 Discussion

- What is the difference between `bash create_project.sh ...` and `./create_project.sh ...`?
- What happens if the script is not executable?

---

## 9. A simple loop script

Create another script:

```bash
nano creating_files.sh
```

Paste this content:

```bash
#!/bin/bash

for number in {1..4}; do
  touch "file_${number}.txt"
done
```

Make it executable and run it:

```bash
chmod +x creating_files.sh
./creating_files.sh
ls
```

### 💬 Discussion

- What is brace expansion in `{1..4}`?
- Why is it good practice to write variables as `${number}` instead of `$number` in scripts?

---

## 10. Optional | Script with if-else conditions

Create one more script:

```bash
nano if_else.sh
```

Paste this content:

```bash
#!/bin/bash

echo "Please enter a number:"
read number

if [ "$number" -gt 10 ]; then
  echo "The number is greater than 10."
elif [ "$number" -lt 10 ]; then
  echo "The number is less than 10."
else
  echo "The number is equal to 10."
fi
```

Run it:

```bash
chmod +x if_else.sh
./if_else.sh
```

### 💬 Discussion

- What happens if you enter `10`?
- What happens if you enter a letter instead of a number?
- Which operators compare numbers in Bash?

---

## 11. Optional | Mini VCF challenge

In `~/2026-Workshop-HSPA-Tunisia/scratch`, do the following:

1. Exclude header lines starting with `#` from the file `A_HA_H3.vcf`
2. Use `cut` to extract the `REF` column
3. Count unique values
4. Save the output in a file called `my_results.txt`

```bash
grep -v "^#" A_HA_H3.vcf | cut -f4 | sort | uniq -c > my_results.txt
```

---

## 📌 Quick reference

| Command | Purpose |
| --- | --- |
| `tar -xvzf` | Extract a `.tar.gz` archive |
| `gzip` | Compress a file |
| `gunzip` | Uncompress a `.gz` file |
| `zcat` | Print contents of a `.gz` file |
| `zless` | View a `.gz` file page by page |
| `grep` | Search text |
| `grep -c` | Count matching lines |
| `cut` | Extract selected columns |
| `sort` | Sort lines |
| `uniq` | Remove consecutive duplicates |
| `wc` | Count lines, words, or characters |
| `chmod +x` | Make a file executable |
| `bash script.sh` | Run a script with Bash |
| `./script.sh` | Run an executable script directly |

---

[⬅ Back to main page](../README.md)