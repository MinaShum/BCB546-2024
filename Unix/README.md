# UNIX Assignment - BCB 546 


## Data Inspection
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
1. The size of the file is 11M. 
2. Last modification date and time was Jan 24 13:25. 
3. It is a .txt file, ASCII text. 
4. The number of lines in this file is 2783. 
5. Contains 986 columns. 

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
1. The size of the file is 81K. 
2. Last modification date and time was Jan 24 13:25. 
3. It is a .txt file, ASCII text. 
4. The number of lines or rows in this file is 984. 
5. Contains 15 columns. 


## Data Processing

### Maize Data
<br>

```
# To inspect the data structure and to make sure the code is working correctly, you can use "awk '{print $(column_index)}' (file_name) | sort | uniq -c" at each step or compare the column and row numbers.

# Separating the files based on the provided strings (Groups) and filter out empty lines
awk '$3 ~ /ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt | grep -v '^$' > maize.txt

# Getting the header of the input file
head -n 1 fang_et_al_genotypes.txt > head1

# Add the header to the raw data produced in previous steps
cat head1 maize.txt > maize2.txt

# Deleting the columns named "Sample_ID" and "JG_OTU". For this analysis only Groups and the chromosomes are needed.
cut -f 3-986 maize2.txt > maize3.txt

# Transpose the dataframe. (So we have each chromosome in a row)
awk -f transpose.awk maize3.txt > fmaize.txt

# Sort both data and header based on the first column before joining. the first column will be the chromosome.
sort -k1,1 fmaize.txt > ffmaize.txt

# Get columns "1","3", and "4" corresponding to "SNP_ID", "Chromosome", and "Position".
awk '{print $1, $3, $4}' snp_position.txt > head2.txt
sort -k1,1 head2.txt > h2.txt
join -1 1 -2 1 ffmaize.txt h2.txt > fm.txt


# Now we can start processing the fm.txt based on the questions. I did this in a separate folder named maize (mkdir maize, cp fm.txt maize, cd maize).


# Get the frequency of each chromosome
awk '{print $2}' fm.txt | sort | uniq -c

# Separate based on each chromosome in a different file named mch_{1-10}.txt
for i in {1..10}; do awk -v num="$i" '$2 == num { print $0 > "mch_"num".txt" }' fm.txt; done

# Using the code below to check the counts
total_sum=0
for i in {1..10}; do
    row_count=$(wc -l "mch_$i.txt" | awk '{print $1}')
    total_sum=$((total_sum + row_count))
done
echo "Total sum of row numbers: $total_sum"

# Separate the files containing "?" and sort based on increasing position
for i in {1..10}; do grep "?" mch_$i.txt | sort -k2,2n > inc_order$i.txt; done

# Replace "?" with "-" and sort based on decreasing position 
for i in {1..10}; do sed 's/?/-/g' inc_order$i.txt | sort -r -k2,2n > dec_order$i.txt; done

# Creating one file for "unknown" sequences
awk '$2 == "unknown" { print > "unknown.txt" }' fm.txt

#Creating one file for "multiple sequences
awk '$2 == "multiple" { print > "multiple.txt" }' fm.txt
```

The explanation is provided in the code block using comments.
<br>
I have 10 files named mch_{1-10}.txt which contain separate chromosomes.
<br>
10 files names inc_order{1-10}.txt for increasing order and "?".
<br>
10 files named dec_order{1-10}.txt for decreasing order and replacing "?" with "-".
<br>
1 file named multiple.txt for "multiple" and 1 named unknown.txt for "unknown".
<br>
You can find the information of each sequence in the first 3 columns.



### Teosinte Data
#### The process for Teosinte is same as Maize so the explanation is skipped.

```
awk '$3 ~ /ZMPBA|ZMPIL|ZMPJA/' fang_et_al_genotypes.txt | grep -v '^$' > teosinte.txt
cat head1 teosinte.txt > teosinte2.txt
cut -f 3-986 teosinte2.txt > teosinte3.txt
awk -f transpose.awk teosinte3.txt > fteosinte.txt
# Sort before joining files
sort -k1,1 fteosinte.txt > ffteosinte.txt
join -1 1 -2 1 h2.txt ffteosinte.txt > ft.txt

mkdir teosinte
cp ft.txt teosinte
cd teosinte

for i in {1..10}; do awk -v num="$i" '$2 == num { print $0 > "mch_"num".txt" }' ft.txt; done
for i in {1..10}; do grep "?" mch_$i.txt | sort -k2,2n > inc_order$i.txt; done
for i in {1..10}; do sed 's/?/-/g' inc_order$i.txt | sort -r -k2,2n > dec_order$i.txt; done
awk '$2 == "unknown" { print > "unknown.txt" }' ft.txt
awk '$2 == "multiple" { print > "multiple.txt" }' ft.txt
```

The explanation is provided in the maize code block using comments.
<br>
I have 10 files named mch_{1-10}.txt which contain separate chromosomes.
<br>
10 files names inc_order{1-10}.txt for increasing order and "?".
<br>
10 files named dec_order{1-10}.txt for decreasing order and replacing "?" with "-".
<br>
1 file named multiple.txt for "multiple" and 1 named unknown.txt for "unknown".
<br>
You can find the information of each sequence in the first 3 columns.
