# SRA Toolkit

There are a lot of data in different repositories in the cloud. A lot of works have been 
putting effort in sequencing genomic data to get the information needed to answer a miryad of 
questions.
One of the biggest repository-pages for genomic/metagenomic data is the National Certer for 
Biotechnology Information (NCBI). Regardless of the big data that NCBI has hoarded in the last 
decade, how to acess and download this data remains troublesome. Users around institutes have 
been facing the inconclusive path of going from a `Bioproject` page, to the `Biosample` 
information, then jumped to the `SRA experiments` inmense list and return finally to the 
`Biosample` page. In order to download the desired data, we need to locate the *Accession* 
number of the sample data, and type it in one of the `SRA Toolkit` tools. This is not trivial (It should be!), so let's go 
to explore `SRA Toolkit`

## fasterq-dump 

In ancient times (before the sra-tools version 2.9.1), `fastq-dump` was the tool used by 
deafult to access public dat in NCBI. But, with the 2.9.1 version, `fasterq-dump` became
available. It is faster than its predecesor and a little bit more intuitive that the former 
(`fastq-dump` was little intuitive). `fastq-dump` is still available, but the line of 
thought of the developers is to erase it sooner than latter.

### Creating a folder for the data
It is important to maintain onder in your projects. This extrapolates to the tools used inside
each one of them. We will create a folder inside or disk where we will save the downloaded data:

~~~
$ mkdir ~/sra-toolkit/data/
~~~
{: .bash}

~~~
$ cd ~/sra-toolkit/data/
~~~
{: .bash}

Inside this folder, we will download the data that we will use the rest of the lesson.

### Accessing to the data

Uploadig the data used in a research project is now a must for the majority of the magazines.
We will go to the [paper-page](https://www.tandfonline.com/doi/full/10.1080/21678421.2021.1904994?scroll=top&needAccess=true) to access to the data-page from the NCBI.
In this page, we will go to the aknowledgements section, were the 
**Data availability statement** is found.

<a href="{{ page.root }}/fig/02-07-01.png">
  <img src="{{ page.root }}/fig/02-07-01.png" alt="Data availability 
  section in the paper-page" />
</a>

###### Figure 1. Data availability section in the paper-page

Here, we will found the `Bioproject` number. This is a identification-code, linked to a page
where the information concerning a project and its data is hoarded. For this paper, the number
is: `PRJNA566436`. 
With this information, we can open a NCBI page in our favorite internet brower, and paste this
code in the search section. Then, a new pag with the search results will be displayed. Here, 
we will choose the `human gut metagenome` option:

<a href="{{ page.root }}/fig/02-07-02.png">
  <img src="{{ page.root }}/fig/02-07-02.png" alt="Search results after using the Bioproject
  code: PRJNA566436" />
</a>


###### Figure 2. Search results after using the Bioproject code: PRJNA566436

In the new displayed page, we can see different pieces of information. We are interested in the
**Project Data** section, which shows in a table, the sequence data. NCBI saves each set of
files (usually libraries) in a `Sequence Read Archive` (SRA). This will be the identifiaction
code that we will search if we want to download the data. We will click on the number of 
**SRA experiments**, which is **18**:

<a href="{{ page.root }}/fig/02-07-03.png">
  <img src="{{ page.root }}/fig/02-07-03.png" alt="SRA Experiments section for accessing
  the libraries information" />
</a>

###### Figure 3. SRA Experiments section for accessing the libraries information

This will lead us to a page where the desired information is still inconspicuous. We will click
on the **Send results to Run selector** option, at the top of the page:

<a href="{{ page.root }}/fig/02-07-04.png">
  <img src="{{ page.root }}/fig/02-07-04.png" alt="Option to display a more informative 
  resume of the data" />
</a>

###### Figure 4. Option to display a more informative resume of the data

The resulting page (SRA Run Selector), will show the information the is located in the 
`Bioproject` and `Biosample` pages. Also, in the bottom we will find a table with useful 
information provided by the authors. In order to obtain the *Accession* info, we will
download a **Accession List** by clicking in this section of the **Select** square:

<a href="{{ page.root }}/fig/02-07-05.png">
  <img src="{{ page.root }}/fig/02-07-05.png" alt="Downloading the Accession List file" />
</a>

###### Figure 5. Downloading the Accession List file

You can obtain this file from [here]().
We will move the new file to our `/data` directory and re-name it as **SRA-names.txt**:


~~~
$ mv ~/Downloads/SRR_Acc_List ~/sra-toolkit/data/SRA-names.txt
$ cat SRA-names.txt
~~~
{: .bash}

~~~
SRR10153499
SRR10153504
SRR10153506
SRR10153508
SRR10153510
SRR10153512
SRR10153514
SRR10153573
SRR10153500
SRR10153501
SRR10153502
SRR10153503
SRR10153505
SRR10153507
SRR10153509
SRR10153511
SRR10153513
SRR10153515
~~~
{: .output}

### Using faterq-dump to download the data

As mentioned at the beggining, `fasterq-dump` is the new version of `fastq-dump`. Let's see 
some of the parameters that this new tool can offer:
~~~
$ fasterq-dump --help
~~~
{: .bash}

First, we will put attention to the split options:

~~~
  -s|--split-spot                  split spots into reads
  -S|--split-files                 write reads into different files
  -3|--split-3                     writes single reads into special file
     --concatenate-reads           writes whole spots into one file
~~~
{: .bash}

#### --split-spot (-s)

This flag will generate a unique file which will contain all the information for the 
library, no matter if those reads are forward or reverse sequenced. Each read will come 
along the 4 lines usual in the `FASTQ` format


#### ---split-file (-S)

With this statement, we will end with separate files for the forward and the reverse reads
(1.fastq and 2.fastq respectively). Nevertheless, the unmated reads (those present in
the forward but without their complement in reverse and visceversa) will also be located
in their respective file. This can be useful for special kind of analyses, but usually we
will prefer to exclude the unmated reads from the next steps. Moreoevee, this option
will write each read with the four lines from the `FASTQ` format.

#### --concatenate-reads

The informatio of each read is concatenated and each new spot (information from the forward
and reverse) is written alongside the four lines characteristic of the `FASTQ` format.

#### --split-spot

This is the deafult option for `fasterq-dump`. The source file is split in a file containing
the forward reads (_i.e._ 1.fastq) and the reverse ones (_i.e._ 2.fastq). Unmated reads are placed in a 3.fastq or SRA-code-name file. Each read is written with the 4 characteristic
lines of the `FASTQ` format.
Most of the sequencing projects are now in paired-end read format. This is also the case for 
the reads that we will use, so this is the most reliable option.

All this options have its reciprocal one in `fastq-dump`, which can be a reference for 
the users accostumed to it:

~~~
fastq-dump SRRXXXXXX --split-3 --skip-technical       fasterq-dump SRRXXXXXX

fastq-dump SRRXXXXXX --split-spot --skip-technical    fasterq-dump SRRXXXXXX --split-spot

fastq-dump SRRXXXXXX --split-files --skip-technical   fasterq-dump SRRXXXXXX --split-files

fastq-dump SRRXXXXXX                                  fasterq-dump SRRXXXXXX --concatenate-reads --include-technical

~~~
{: .bash}

Let's run one example with the first accession of our data: SRR10153499. We will use the 
`--stdout` option, to not save the output in a file, but only to be displayed in the 
terminal. Also, we will use some of the commands that we reviewed in the past lessons:

~~~
fasterq-dump --stdout SRR10153499 --concatenate-reads --include-technical | head -n 8
~~~
{: .bash}


~~~
@SRR10153499.1 1 length=453
TACGGAGGATACGAGCGTTATCCGGATTTATTGGGTTTAAAGGGTGCGCAGGTGGTCCTGCAAGTCAGTGGTGAAAAGCTGAGGCTCAACCTCAGCCTTGCCGTTGAAACTGCAAGACTTGAGAGTACATGATGTGGGCGGAATGCGTAGTGTAGCGGTGAAATGCATAGATATTACGCAGAACTCCGATTGCGAAGGCAGCTCACAAAGGTATATCTGACACTGAGGCACGAAAGCGTGGGGAGCAAACCCTGTTTGCGCCCCACGCTTTCGTGCCTCAGTGTCAGATATACCTTTGTGAGCTGCCTTCGCAATCGGAGTTCTGCGTAATATCTATGCATTTCACCGCTACACTACGCATTCCGCCCACATCATGTACTCTCAAGTCTTGCAGTTTCAACGGCAAGGCTGAGGTTGAGCCTCAGCTTTTCACCACTGACTTGCAGGACCACC
+SRR10153499.1 1 length=453
CCCCCCBCCFFFGGGGGGGGGGHGGGGGHHHHHHHGGGHHHHHGHGGGGGGGHHGGHHHHHHHHHHHHHHHHGHHHHHHGHGHGFHHHHHHHHHHHGHHHHHGGGGHHHHHHHHHHGHHHHHHGHHHHHHHHHHHHHGGGGGGGHHGGGGGHHHHHGGGGGGHHHHFHHHHHFHHHHGGGGGGGFGGGGGAEGGGBGGFFFFFFFFBFAFF0BFFFFFFFFFFFFFFFFFFFFFFFEFFFFFFFFB9:BA>AAA1B3C@1AAGEGGGGGF0B0BBAFG1FGFGGHHHHHDGBEGFHHHDADAFGFGHFGGFF//EFFCBEFHBGHFFEAEGHFHGBFGF2GHHHEGHGGG>EHFHHFGG?EFHFGCA@CGGHHFGFH2FFHGFFHHGHHHHHHFHHHHFH1ACC@-.<F1C0FFCFH/GFGHHFHCGGHHCCCC0GCGFGEGGH0CG.C9..C
@SRR10153499.2 2 length=426
TACGGAAGGTTCGGGCGTTATCCGGATTTATTGGGTTTAAAGGGAGCGTAGGCCGTTTGGTAAGCGTGTTGTGAAATGTAGTAGCTCAACTTCTAGATTGCAGCGCGAACTGTCAGACTTGAGTGCGCACAACGTAGGCGGAATTCATGGTGTAGCGGTGAAATGCTTAGATATCATGAAGAACTCCGATTGCGAAGGCAGCTTACGGGAGCCTGTTTGATACCCGCACTTTCGAGCATCAGCGTCAGTTGCGCTCCCGTAAGCTGCCTTCGCAATCGGAGTTCTTCATGATATCTAAGCATTTCACCGCTACACCATGAATTCCGCCTACGTTGTGCGCACTCAAGTCTGACAGTTCGCGCTGCAATCTAGAAGTTGAGCTACTACATTTCACAACACGCTTACCAAACGGCCTACGCTCCCTTT
+SRR10153499.2 2 length=426
CCCCCAAAAFFFGGGGGGGGGGHGGGGGHHHHHHHGGGBGHHHGHGGGGGGGHHGGGHGGHGHHHGGGGHHHGHHHHHHHHHHHGHHHHHGHHHFHHHHHHGFHGGGDGGHHHHHHEGHHHEHHHHGGGGGHGDHGHGHGGGFFHHGGHHHHHHHHGGGGEGGGGGGGGGE0FFBCFG0CFGGGGGGGAGFEEFA-CDEFDFFFFFFFF.;>AAAAFFFFFFFGGCEGAEG4BAABBEGFFHHGGGGGHHHHGGGGG1EGCFGHHHHGGFGHGEEGHGGEFEHHGHGBFFFHGGGG44F3GFHHHGHHGEE>EGGCHHHHFGHHF?EDGAHGHFAF<2?CCDGHHHHHFGGHHHG<FBGD@DFFG/0DFHDGD0GDDGFC::;:0:BFHHBB0GG0.CEEA.C.FFFBAAA;-99FFAFAD?.BBF
~~~
{: .output}




~~~

~~~
{: .bash}