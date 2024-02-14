<br>
<br>
# UNIX Assignment - BCB 546


## Data Inspection

### Mina Shumaly <br>
### Attributes of `fang_et_al_genotypes`

```
ll -h #ls -lh fang_et_al_genotypes.txt
stat fang_et_al_genotypes.txt
file fang_et_al_genotypes.txt
wc -l fang_et_al_genotypes.txt
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
```
-rw-r--r--. 1 mshumaly domain users 11M Jan 24 13:25 fang_et_al_genotypes.txt
<br>
-rw-r--r--. 1 mshumaly domain users 81K Jan 24 13:25 snp_position.txt
<br>
-rw-r--r--. 1 mshumaly domain users 353 Jan 24 13:25 transpose.awk
<br>
Rw: owner – r: only read permissions – 1: number of hard links to the file – file dimensions – last modification date and time – name of the file
<br>
<br>

fang_et_al_genotypes.txt: ASCII text, with very long lines
<br>
<br>

By inspecting this file I learned that:
<br>
1. The size of the file is 11M. <br>
2. Last modification date and time was Jan 24 13:25. <br>
3. It is a .txt file, ASCII text. <br>
4. The number of lines in this file is 2783. <br>
5. Contains 986 columns. <br>
<br>
<br>
### Attributes of `snp_position.txt`

```
ll -h #ls -lh snp_position.txt
stat snp_position.txt
file snp_position.txt
awk 'END {print NR}' snp_position.txt
awk -F "\t" '{print NF; exit}' snp_position.txt
```
-rw-r--r--. 1 mshumaly domain users 81K Jan 24 13:25 snp_position.txt
<br>
<br>
snp_position.txt: ASCII text
<br>
<br>

By inspecting this file I learned that:
<br>

1. The size of the file is 81K. <br>
2. Last modification date and time was Jan 24 13:25. <br>
3. It is a .txt file, ASCII text. <br>
4. The number of lines or rows in this file is 984. <br>
5. Contains 15 columns. <br>
<br>
<br>

## Data Processing
<br>

### Maize Data
<br>

```
# Separating the files based on the provided strings
awk '$3 ~ /ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt | grep -v '^$' > maize.txt
# Getting the header of the file
head -n 1 fang_et_al_genotypes.txt > head1
# Add the header to the raw data produced before
cat head1 maize.txt > maize2.txt
# Deleting the columns that we don't need their information
cut -f 3-986 maize2.txt > maize22.txt
# Transpose the dataframe
awk -f transpose.awk maize22.txt > fmaize.txt
# Sort based on the first column before adding the header
sort -k1,1 fmaize.txt > ffmaize.txt
sort -k1,1 head2.txt > h2.txt
join -1 1 -2 1 ffmaize.txt h2.txt > fm.txt

# Now we can start processing the fm.txt based on the question. I did this in a separate folder named maize.
# Get the frequency of each chromosome
awk '{print $1575}' fm.txt | sort | uniq -c
# Separate based on each chromosome in a different file named mch_{1-10}.txt
for i in {1..10}; do     awk -v num="$i" '$1575 == num { print $0 > "mch_"num".txt" }' fm.txt; done
# Using the code below just to check the counts
total_sum=0
for i in {1..10}; do
    row_count=$(wc -l "mch_$i.txt" | awk '{print $1}')
    total_sum=$((total_sum + row_count))
done
echo "Total sum of row numbers: $total_sum"
# Separate the files containing "?" and sort based on increasing position
for i in {1..10}; do    grep "?" mch_$i.txt | sort -k1576,1576n > inc$i.txt; done
# Replace "?" with "-" and sort based on decreasing position 
for i in {1..10}; do
    sed 's/?/-/g' inc$i.txt | sort -r -k1576,1576n > dec$i.txt
done
# Creating one file for "unknown" sequences
awk '$1575 == "unknown" { print > "unknown.txt" }' fm.txt
#Creating one file for "multiple sequences
awk '$1575 == "multiple" { print > "mul.txt" }' fm.txt
```

The explanation is provided in the code block using comments.
<br>
I have 10 files named mch_{1-10}.txt which contain separate chromosomes.
<br>
10 files names inc{1-10}.txt for increasing order and "?".
<br>
10 files named dec{1-10}.txt for decreasing order and replacing "?" with "-".
<br>
1 file named mul.txt for "multiple" and 1 named unknown.txt for "unknown".
<br>
The chromosome and position are in the last two columns.
<br>
<br>


### Teosinte Data

```
# Separating the files based on the provided strings
awk '$3 ~ /ZMPBA|ZMPIL|ZMPJA/' fang_et_al_genotypes.txt | grep -v '^$' > teosinte.txt
# Getting the header of the file
head -n 1 fang_et_al_genotypes.txt > head1
# Add the header to the raw data produced before
cat head1 teosinte.txt > teosinte2.txt
# Deleting the columns that we don't need their information
cut -f 3-986 teosinte2.txt > teosinte22.txt 
# Transpose the dataframe
awk -f transpose.awk teosinte22.txt > fteosinte.txt
# Sort based on the first column before adding the header
sort -k1,1 fteosinte.txt > ffteosinte.txt
sort -k1,1 head2.txt > h2.txt
join -1 1 -2 1 ffteosinte.txt h2.txt > ft.txt

# Now we can start processing the ft.txt based on the question. I did this in a separate folder named teosinte.
# Separate based on each chromosome in a different file named mch_{1-10}.txt
for i in {1..10}; do     awk -v num="$i" '$977 == num { print $0 > "mch_"num".txt" }' ft.txt; done
# Separate the files containing "?" and sort based on increasing position
for i in {1..10}; do    grep "?" mch_$i.txt | sort -k978,978n > inc$i.txt; done
# Replace "?" with "-" and sort based on decreasing position 
for i in {1..10}; do
    sed 's/?/-/g' inc$i.txt | sort -r -k978,978n > dec$i.txt
done
# Creating one file for "unknown" sequences
awk '$977 == "unknown" { print > "unknown.txt" }' ft.txt
#Creating one file for "multiple sequences
awk '$977 == "multiple" { print > "mul.txt" }' ft.txt
```

The explanation is provided in the code block using comments.
<br>
I have 10 files named mch_{1-10}.txt which contain separate chromosomes.
<br>
10 files names inc{1-10}.txt for increasing order and "?".
<br>
10 files named dec{1-10}.txt for decreasing order and replacing "?" with "-".
<br>
1 file named mul.txt for "multiple" and 1 named unknown.txt for "unknown".
<br>
The chromosome and position are in the last two columns.
<br>
<br>

