# NCBI_Companion
## Introduction

NCBI_Companion targets to construct databases, generate a fasta file and a taxonomy mappingusing by using Genbank Entrez utilities.

Biopython copyright belongs to Biopython teams
Entrez copyright belongs to NCBI entrez teams
***
## Contents

###class LoadSpecies

>This class is to used fetch species name contains in csv file, text file, excel or fasta file.

**Functions**

| function names | parameters | Return |
|--------------------|-----------------|-------|
| LoadSpecies | species_input, species_output, ifout | initialize |
| ReadSpeciesFile_text | None | a list  (if specified, a table in local directory) |
| ReadSpeciesFile_excel | sp_col, sheetname, header, fullname | a list (or a local file) |
| ReadSpeciesFile_csv | sp_col, header, fullname | a list (or a local file ) |
| ExtractSpeciesFromFasta | ranges, delimiter | a list (or a local file) |

>all those functions return a list of species names or ID list

**Usage**

```python
species = LoadSpecies(input_file, output_file, ifout = True)
species.ReadSpeciesFile_excel(sp_col='act_sym_fullname', sheetname = 0, header = 0,  fullname = False)
```

***
###class NCBI_Tools
> This class implements Biopython Entrez to interact with NCBI/Genbank API to either get accession id, taxonomy id, sequences or taxonomy ranking, or converting them. Some of core functions return a tracker flag that can trace which functions have been called. This order will be used by Sqlite_Dumps class to generate fasta file and mapping file

**Functions**

| Functions | parameters | return | database table name | Tracker Flag |
|---|---|---|---|---|
| NCBI_Tools | ncbi_key, ncbi_email, sqlite_db, ncbi_db, idtype | initialize | None | None |
| Update_API | key, email | updated key and email | None | None |
| getTracker | None | a tracker list | None | None |
| ncbi_Species2Genome | species list | accession ID list | Sp2Genome | P9 |
| ncbi_Search2Acc | search terms, howmany to quire | Search2AccIDs |Tracker | P1 |
| ncbi_Species2Acc | species list, more terms (optional) | Sp2AccIDs | Tracker | P2 |
| ncbi_GetSeqsFromAcc | table_name, column_name | Acc2Seq | Tracker | P3 |
| ncbi_GetTaxIdFromAcc | table_name, column_name | Uid2TaxIDs | Tracker | P4 |
| ncbi_eAcc2Seq | accession ID | Tracker | Acc2Seq | P5 |
| ncbi_eAcc2TaxID | accession ID | Tracker | Uid2TaxIDs | P6 |
| ncbi_Species2Taxa | species list, style, levels_n | Tracker | Sp2Taxa | P7 |
| ncbi_Id2Taxa | style, levels_n | Tracker | TaxId2Ranking | P8 |


**Usage**

Example 1: Known species list and search for its trnL gene

Tracker Flag: P2---> P3 / P4 ---> P4 / P3 ---> P8 

```python
import NCBI_Companion
companion = NCBI_Companion.NCBI_Tools(key, email, 'databasename', 'nuccore', 'acc')
companion.ncbi_Species2Acc(species_list, 'trnL')
companion.ncbi_GetSeqsFromAcc('Sp2AccIDs', 'acc_id')
companion.ncbi_GetTaxIdFromAcc('Sp2AccIDs', 'acc_id')
companion.ncbi_Id2Taxa(style = 'qiime', levels_n = 7) # 7 levels of taxonomy ranking qiime style D_0_..;
```


### class Sqlite_Dumps

**Functions**

| functions | parameters | return |
|---|---|---|
| Sqlite_Dumps | sqlite_db, output_prefix, tracker, header_type = 'acc' | initialize |
| sqlite_dump | None | a fasta and a mapping file in local |


**Usage**

```python
# get tracker
tracker = companion.getTracker()
dump = Sqlite_Dumps('database constructed by NCBI_Tools', 'fasta and mapping file name, not extension', tracker, header_type = 'acc')
```




