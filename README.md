# Fairheart-in-the-cloud-with-Galaxy
Project of bio-informatics students at Avans university of applied sciences. It is based on buidling and designing a private galaxy server for storage and analysis of GDPR sensitive data.

In this Github is the following present
- a tutorial with which commands are used for building the Ansible Galaxy server
- a list of errors encouterd and how to solve
- the transcriptromics RNA-seq data pathway


## overview of building the galaxy server

to create the Ansible galaxy server there are a few things what must be done
- get a domain name
- get acces to VM resources
- prepare the VM
- prepare the VM for Galaxy installation
- Install the required software for Galaxy
- install Galaxy flavours

![alt text](https://github.com/NielsMol/Fairheart-in-the-cloud-with-Galaxy/blob/master/overview%20building%20galaxy%20instance.png)

for complete explaination which tutorial to follow or what to do refer to this flowchart


![alt text](https://github.com/NielsMol/Fairheart-in-the-cloud-with-Galaxy/blob/master/Flowchart%20building%20galaxy%20instance.png)

## Transcriptomics RNA seq

The RNA data will be uploaded by Galaxy and tools installed to preform it.\
it starts with SRA number and then download and exstract reads in FASTQ format.\
Followed the data will be checked by FASTQC. if the data is of low quality the workflow will be terminated.\
If the data is of good enough quality the adapters from Illumia seq will be cut off.\
Followed a map will be build with the HISAT2 tool and checked with various tools like:
- Infer experiment, is the sanity check
- Mark duplicated will remove the duplicated data by PCR errors
- Idxstats ios overal statistiscs
- Genebody coverage explain how much of the RNA seq covers the reference genome
- Read distribution will statics on how great the data is distrubuted

the data will be checked by Feature count to see which genes are active\
Limma-Voom tool wil calculate the differental expresiion\
Goseq wukk establish Biological pathway and enrichment where\
a heatmap, volcanoplot and cummeRbund can be build\
with vatrious data can all be combined in one single file with the MultiQC tool\

![alt text](https://github.com/NielsMol/Fairheart-in-the-cloud-with-Galaxy/blob/master/overview%20transcriptomics.png)
