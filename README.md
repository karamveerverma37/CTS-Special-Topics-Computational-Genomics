# CTS-Special-Topics-Computational-Genomics
This Repository contains code for a course "Special Topics: Computational  Genomics" that we teach at Penn State College of Medicine.

# Shell Scripting and High-Performance Computing for Genomics

---

## Welcome to Your Bioinformatics Journey! 🧬💻

If you're reading this, you're about to gain a superpower that will transform how you do research. You already understand the beautiful complexity of biology—now you're going to learn how to analyze it at a scale that would have been impossible just a generation ago. Welcome to the world where biology meets computation!

---

## Why Should You Be Excited?

**Think about this:** The human genome contains about 3 billion base pairs. If you tried to read one base pair per second, it would take you 95 years to read it once. But with the skills you're learning, you can analyze entire genomes in hours or days. You're not just learning commands—you're learning to work at the speed and scale of modern genomic science.

**What you'll be able to do:**
- Analyze thousands of sequences in the time it used to take to analyze one
- Run the same analysis on 100 samples with a single command
- Process next-generation sequencing data that would fill thousands of books
- Submit jobs that run while you sleep and have results waiting in the morning
- Reproduce your analyses perfectly, making your research more reliable

---

## Part 1: The Shell - Your New Laboratory Interface

### What is the Shell?

The shell (also called the command line or terminal) is how you talk directly to a computer. Instead of clicking icons, you type commands. This might feel strange at first, but it's incredibly powerful.

**Think of it this way:** Using a graphical interface (clicking) is like ordering from a menu at a restaurant. Using the shell is like being in the kitchen yourself—you have complete control and can create exactly what you need.

### Why Biologists Need the Shell

Most bioinformatics tools don't have graphical interfaces. They're designed to process massive datasets efficiently, and the shell is the perfect environment for this. Plus, when you're working with high-performance computing clusters (which you'll need for big genomic datasets), the shell is your primary interface.

---

### Essential Shell Concepts

#### 1. Navigating Your File System

Your computer's files are organized like a tree. You need to know where you are and how to move around.

**Key commands:**
- `pwd` (print working directory) - Where am I?
- `ls` (list) - What's in this folder?
- `cd` (change directory) - Move to a different folder

**Example workflow:**
```bash
pwd                          # Shows: /home/username
ls                           # Shows your files and folders
cd genomics_project          # Move into your project folder
ls                           # See what's in your project
cd ..                        # Go back up one level
```

**Biological analogy:** Think of `pwd` as asking "What tissue am I looking at?", `ls` as "What cell types are here?", and `cd` as moving your microscope to a different sample.

---

#### 2. Working with Files

**Key commands:**
- `cat` - Display file contents (good for small files)
- `head` - Show the first lines of a file
- `tail` - Show the last lines of a file
- `less` - Browse through a file (press `q` to quit)
- `wc` - Count lines, words, characters

**Example with a FASTA file:**
```bash
head -n 4 sequences.fasta    # Look at first 4 lines
wc -l sequences.fasta        # How many lines?
less sequences.fasta         # Browse the whole file
```

**Why this matters:** Before running a 3-hour analysis, you want to quickly check that your input file looks correct!

---

#### 3. Creating and Manipulating Files

**Key commands:**
- `mkdir` - Make a new directory/folder
- `cp` - Copy files
- `mv` - Move or rename files
- `rm` - Remove files (be careful!)

**Example: Organizing a sequencing project**
```bash
mkdir raw_data              # Create folder for raw sequences
mkdir results               # Create folder for results
cp *.fastq raw_data/        # Copy all FASTQ files to raw_data
mv old_name.txt new_name.txt # Rename a file
```

**Important safety tip:** There's no "undo" or recycle bin with `rm`. Always double-check before deleting!

---

#### 4. The Power of Wildcards

The `*` symbol is a wildcard that matches anything. This is where the shell starts to show its power.

**Examples:**
```bash
ls *.fasta                  # List all FASTA files
ls sample_*.fastq           # List all files starting with "sample_"
ls *_R1.fastq              # List all R1 (forward) reads
cp *.fasta sequences/       # Copy all FASTA files to sequences folder
```

**The magic:** Instead of copying 100 files one at a time by clicking, you copy them all with one command!

---

### Essential Text Processing Tools

Genomic data is mostly text (FASTA, FASTQ, VCF, GFF, etc.). These tools let you process that text powerfully.

#### 1. grep - Search for Patterns

`grep` finds lines containing specific text. It's like Ctrl+F but much more powerful.

**Examples:**
```bash
grep "ATGC" sequences.fasta          # Find sequences containing ATGC
grep ">" sequences.fasta             # Find all sequence headers
grep -c ">" sequences.fasta          # Count sequences (count header lines)
grep "gene" annotation.gff           # Find all lines mentioning "gene"
```

**Real-world use:** "Which samples have coverage above 30X?" `grep` can find them instantly in your QC report.

---

#### 2. cut - Extract Columns

Many genomics files are tab-delimited (like a spreadsheet). `cut` extracts specific columns.

**Example with a VCF (variant call format) file:**
```bash
cut -f 1,2 variants.vcf     # Extract columns 1 and 2 (chromosome and position)
cut -f 1-3 variants.vcf     # Extract columns 1 through 3
```

---

#### 3. sort and uniq - Organize and Count

**Examples:**
```bash
sort samples.txt                    # Sort lines alphabetically
sort -n numbers.txt                # Sort numerically
sort -k2,2n data.txt              # Sort by column 2, numerically

uniq sequences.txt                 # Remove duplicate lines
sort sequences.txt | uniq -c       # Count occurrences of each sequence
```

**Real application:** Finding the most common k-mers in your sequences, or counting how many reads mapped to each chromosome.

---

#### 4. Pipes - Chain Commands Together

The pipe symbol `|` takes the output of one command and feeds it into another. This is where magic happens.

**Example: Count unique sequence IDs**
```bash
grep ">" sequences.fasta | wc -l
```
This reads as: "Find all headers, then count the lines."

**More complex example:**
```bash
grep ">" sequences.fasta | cut -f 1 -d " " | sort | uniq -c
```
This reads as: "Find headers, extract the first field, sort them, count unique ones."

**Why this is amazing:** You're combining simple tools to answer complex questions, without writing any code!

---

### Shell Scripts - Automating Your Work

A shell script is just a file containing commands you'd type manually. But scripts let you:
- Run the same analysis on many samples
- Document exactly what you did (reproducibility!)
- Share your workflow with collaborators
- Avoid repetitive typing and mistakes

#### Your First Script

Create a file called `analyze_sequence.sh`:

```bash
#!/bin/bash
# This script analyzes a FASTA file

echo "Starting analysis..."
echo "Number of sequences:"
grep -c ">" $1
echo "File size:"
ls -lh $1
echo "First sequence:"
head -n 2 $1
echo "Analysis complete!"
```

Run it:
```bash
bash analyze_sequence.sh my_sequences.fasta
```

**What's happening:**
- `#!/bin/bash` - Tells the computer this is a bash script
- `#` lines are comments (notes to yourself)
- `$1` means "the first argument" (your filename)
- `echo` prints messages

---

#### Variables - Store Information

```bash
#!/bin/bash

# Store values in variables
SAMPLE_NAME="patient_001"
INPUT_FILE="data/${SAMPLE_NAME}.fastq"
OUTPUT_DIR="results/${SAMPLE_NAME}"

# Use variables (with $ in front)
mkdir -p $OUTPUT_DIR
echo "Processing $SAMPLE_NAME..."
# (your analysis commands would go here)
echo "Results saved to $OUTPUT_DIR"
```

**Why this helps:** Change the sample name in one place, and everything else updates automatically.

---

#### Loops - Repeat Operations

This is where you really save time. Instead of running an analysis 50 times manually, use a loop.

**Basic loop structure:**
```bash
for VARIABLE in list_of_items
do
    # commands using $VARIABLE
done
```

**Example: Process all FASTQ files**
```bash
#!/bin/bash

for FILE in *.fastq
do
    echo "Processing $FILE..."
    # Count reads
    echo "Number of reads: $(grep -c "^@" $FILE)"
    # (More analysis would go here)
done

echo "All files processed!"
```

**Real-world example: Quality control for all samples**
```bash
#!/bin/bash

for SAMPLE in sample_01 sample_02 sample_03 sample_04
do
    echo "Running FastQC on $SAMPLE..."
    fastqc ${SAMPLE}_R1.fastq ${SAMPLE}_R2.fastq -o qc_results/
done

echo "Quality control complete for all samples!"
```

**The payoff:** This runs QC on 4 samples (8 files) automatically. Scale this to 100 samples, and you've saved yourself hours of repetitive clicking!

---

#### Conditional Statements - Make Decisions

Sometimes you want to do different things based on conditions.

```bash
#!/bin/bash

FILE="sequences.fasta"

if [ -f $FILE ]; then
    echo "File exists! Processing..."
    grep -c ">" $FILE
else
    echo "Error: File not found!"
fi
```

**Practical example:**
```bash
#!/bin/bash

for FILE in *.fastq
do
    READ_COUNT=$(grep -c "^@" $FILE)
    
    if [ $READ_COUNT -lt 1000000 ]; then
        echo "WARNING: $FILE has only $READ_COUNT reads (low coverage)"
    else
        echo "$FILE looks good: $READ_COUNT reads"
    fi
done
```

This automatically flags samples with suspiciously low read counts!

---

### Best Practices for Scripts

1. **Comment your code!** Your future self (in 6 months) will thank you.

```bash
# Bad (no explanation)
grep ">" $1 | wc -l

# Good (clear purpose)
# Count the number of sequences in the FASTA file
grep ">" $1 | wc -l
```

2. **Use meaningful variable names**

```bash
# Confusing
f="data.txt"

# Clear
INPUT_FILE="patient_samples.txt"
```

3. **Check that files exist before processing**

```bash
if [ ! -f $INPUT_FILE ]; then
    echo "Error: Cannot find $INPUT_FILE"
    exit 1
fi
```

4. **Add progress messages**

```bash
echo "Step 1/3: Aligning reads..."
# alignment command
echo "Step 2/3: Calling variants..."
# variant calling command
echo "Step 3/3: Filtering results..."
# filtering command
echo "Pipeline complete!"
```

---

## Part 2: High-Performance Computing (HPC)

### Why HPC Matters for Genomics

**The challenge:** Modern sequencing generates massive amounts of data. A single human genome sequencing run can produce 100+ GB of data. Aligning those reads to a reference genome might take 20+ hours on your laptop.

**The solution:** High-performance computing clusters—many powerful computers working together. Your 20-hour job might finish in 2 hours on an HPC cluster.

---

### What is an HPC Cluster?

Think of it as a building full of powerful computers (called "nodes") all connected together. Instead of one computer doing all the work, you can use dozens or hundreds simultaneously.

**Key components:**
- **Login node:** Where you connect and prepare your work (don't run big jobs here!)
- **Compute nodes:** Where the actual work happens
- **Scheduler:** A system that manages who gets to use which computers when
- **Shared storage:** All nodes can access the same files

**Analogy:** Think of it like a research building. The login node is the lobby where you prepare. The compute nodes are specialized labs. The scheduler is like the building coordinator who assigns lab space. The shared storage is like a shared freezer everyone can access.

---

### Connecting to an HPC Cluster

You typically connect using SSH (Secure Shell):

```bash
ssh username@cluster.university.edu
```

Once connected, you're using a shell, just like before! Everything you learned still applies.

---

### The Job Scheduler - SLURM

Most HPC clusters use a scheduler called SLURM (Simple Linux Utility for Resource Management). You don't run jobs directly—you submit them to SLURM, which runs them when resources are available.

**Key SLURM commands:**
- `sbatch` - Submit a job
- `squeue` - See the job queue
- `scancel` - Cancel a job
- `sinfo` - See available resources

---

### Your First SLURM Job

Create a file called `my_first_job.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=my_analysis      # Name for your job
#SBATCH --time=01:00:00             # Time limit (1 hour)
#SBATCH --mem=4G                    # Memory needed (4 GB)
#SBATCH --cpus-per-task=1           # Number of CPU cores
#SBATCH --output=my_job_%j.out      # Output file (%j = job ID)

# Your commands go here
echo "Job started at $(date)"
echo "Running on node: $(hostname)"

# Example analysis
grep -c ">" sequences.fasta

echo "Job finished at $(date)"
```

Submit it:
```bash
sbatch my_first_job.sh
```

Check its status:
```bash
squeue -u your_username
```

**What happened:** SLURM took your job, waited for resources to be available, ran it on a compute node, and saved the output to a file.

---

### SLURM Directives Explained

The lines starting with `#SBATCH` tell SLURM what resources you need:

```bash
#SBATCH --job-name=align_reads      # Descriptive name
#SBATCH --time=12:00:00             # Maximum run time (12 hours)
#SBATCH --mem=32G                   # Memory (32 GB)
#SBATCH --cpus-per-task=8           # CPU cores (8 cores)
#SBATCH --output=logs/align_%j.out  # Where to save output
#SBATCH --error=logs/align_%j.err   # Where to save errors
```

**Important tips:**
- **Don't request too little time/memory:** Your job will be killed if it exceeds limits
- **Don't request too much:** You'll wait longer in the queue
- **Start with estimates, then adjust:** Run a small test to see what you actually need

---

### Real Genomics Workflow on HPC

Let's say you need to align sequencing reads for 50 samples. Here's how you'd do it:

**Step 1: Create a job script (`align_sample.sh`)**

```bash
#!/bin/bash
#SBATCH --job-name=align
#SBATCH --time=08:00:00
#SBATCH --mem=32G
#SBATCH --cpus-per-task=8
#SBATCH --output=logs/align_%A_%a.out
#SBATCH --array=1-50                # Run 50 jobs (one per sample)

# Load required software
module load bwa
module load samtools

# Get sample name for this array task
SAMPLE=$(sed -n "${SLURM_ARRAY_TASK_ID}p" sample_list.txt)

echo "Processing sample: $SAMPLE"

# Run alignment
bwa mem -t 8 reference.fasta \
    ${SAMPLE}_R1.fastq \
    ${SAMPLE}_R2.fastq \
    | samtools sort -o ${SAMPLE}.bam

echo "Alignment complete for $SAMPLE"
```

**Step 2: Create a list of sample names (`sample_list.txt`)**
```
sample_001
sample_002
sample_003
...
sample_050
```

**Step 3: Submit the job**
```bash
sbatch align_sample.sh
```

**What happens:** SLURM creates 50 separate jobs, one for each sample. They all run in parallel (as resources allow). What might have taken you weeks of manual work happens automatically in hours!

---

### Job Arrays - Process Multiple Samples

Job arrays are perfect for bioinformatics. The key variables:
- `$SLURM_ARRAY_TASK_ID` - The current array index (1, 2, 3, ...)
- `$SLURM_ARRAY_JOB_ID` - The main job ID
- `%A` in filename - Job ID
- `%a` in filename - Array task ID

**Example: Running FastQC on 100 samples**

```bash
#!/bin/bash
#SBATCH --array=1-100
#SBATCH --time=01:00:00
#SBATCH --mem=4G
#SBATCH --output=logs/fastqc_%A_%a.out

module load fastqc

# Get this task's sample
SAMPLE=$(sed -n "${SLURM_ARRAY_TASK_ID}p" samples.txt)

echo "Running FastQC on $SAMPLE"
fastqc ${SAMPLE}_R1.fastq ${SAMPLE}_R2.fastq -o qc_results/
echo "Complete!"
```

---

### Monitoring Your Jobs

**Check your jobs:**
```bash
squeue -u $USER                     # Your running/pending jobs
squeue -u $USER | wc -l            # How many jobs?
```

**Check a specific job:**
```bash
squeue -j 12345                    # Status of job 12345
```

**Cancel jobs:**
```bash
scancel 12345                      # Cancel specific job
scancel -u $USER                   # Cancel all your jobs
scancel -u $USER -t PENDING        # Cancel pending jobs only
```

**Check completed job efficiency:**
```bash
seff 12345                         # See how much memory/time was actually used
```

This helps you tune your resource requests!

---

### Environment Modules

HPC clusters have many software packages installed. You load the ones you need using modules:

**Common commands:**
```bash
module avail                       # See all available software
module avail blast                 # Search for BLAST
module load blast/2.12.0          # Load BLAST version 2.12.0
module list                        # See what you've loaded
module unload blast               # Unload BLAST
module purge                       # Unload everything
```

**In your job script:**
```bash
#!/bin/bash
#SBATCH directives...

# Load software
module load bwa/0.7.17
module load samtools/1.15
module load python/3.9

# Now run your analysis
bwa mem ...
```

---

### Best Practices for HPC

1. **Test on small data first**
   - Run your pipeline on 1-2 samples before launching 100 jobs
   - Catches errors early and helps you estimate resources

2. **Organize your directories**
```
project/
├── raw_data/          # Original sequencing files
├── scripts/           # Your SLURM scripts
├── logs/              # Job output and error logs
├── results/           # Analysis results
│   ├── alignments/
│   ├── variants/
│   └── qc/
└── reference/         # Reference genomes, annotations
```

3. **Use meaningful job names and output files**
```bash
#SBATCH --job-name=bwa_align_${SAMPLE}
#SBATCH --output=logs/align_${SAMPLE}_%j.out
```

4. **Monitor disk usage**
   - Genomics data is huge!
   - Compress files when possible (`gzip`)
   - Delete intermediate files you don't need
   
```bash
du -sh *                # Check folder sizes
df -h                   # Check disk space
```

5. **Document your workflow**
   - Keep a README file explaining your pipeline
   - Comment your scripts
   - Record software versions used

---

## Part 3: Putting It All Together - A Complete Genomics Pipeline

Let's walk through a realistic RNA-seq analysis pipeline that uses everything you've learned.

### The Goal
Analyze RNA-seq data from 20 samples to identify differentially expressed genes.

### The Pipeline Steps
1. Quality control (FastQC)
2. Trim adapters (Trimmomatic)
3. Align to reference (HISAT2)
4. Count reads per gene (featureCounts)
5. Differential expression analysis (DESeq2 in R)

---

### Step 0: Organization

```bash
# Create directory structure
mkdir -p rnaseq_project/{raw_data,scripts,logs,results/{qc,trimmed,aligned,counts}}
cd rnaseq_project

# Create sample list
ls raw_data/*_R1.fastq.gz | sed 's/_R1.fastq.gz//' | sed 's/raw_data\///' > samples.txt
```

---

### Step 1: Quality Control

**Script: `01_fastqc.sh`**
```bash
#!/bin/bash
#SBATCH --job-name=fastqc
#SBATCH --array=1-20
#SBATCH --time=01:00:00
#SBATCH --mem=4G
#SBATCH --output=logs/fastqc_%A_%a.out

module load fastqc

# Get sample for this array task
SAMPLE=$(sed -n "${SLURM_ARRAY_TASK_ID}p" samples.txt)

echo "Running FastQC on $SAMPLE"

# Run QC
fastqc raw_data/${SAMPLE}_R1.fastq.gz raw_data/${SAMPLE}_R2.fastq.gz \
       -o results/qc/

echo "FastQC complete for $SAMPLE"
```

Submit: `sbatch 01_fastqc.sh`

---

### Step 2: Trim Adapters

**Script: `02_trim.sh`**
```bash
#!/bin/bash
#SBATCH --job-name=trim
#SBATCH --array=1-20
#SBATCH --time=02:00:00
#SBATCH --mem=8G
#SBATCH --cpus-per-task=4
#SBATCH --output=logs/trim_%A_%a.out

module load trimmomatic

SAMPLE=$(sed -n "${SLURM_ARRAY_TASK_ID}p" samples.txt)

echo "Trimming $SAMPLE"

trimmomatic PE -threads 4 \
    raw_data/${SAMPLE}_R1.fastq.gz \
    raw_data/${SAMPLE}_R2.fastq.gz \
    results/trimmed/${SAMPLE}_R1_paired.fastq.gz \
    results/trimmed/${SAMPLE}_R1_unpaired.fastq.gz \
    results/trimmed/${SAMPLE}_R2_paired.fastq.gz \
    results/trimmed/${SAMPLE}_R2_unpaired.fastq.gz \
    ILLUMINACLIP:adapters.fa:2:30:10 \
    LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

echo "Trimming complete for $SAMPLE"
```

---

### Step 3: Alignment

**Script: `03_align.sh`**
```bash
#!/bin/bash
#SBATCH --job-name=align
#SBATCH --array=1-20
#SBATCH --time=06:00:00
#SBATCH --mem=32G
#SBATCH --cpus-per-task=8
#SBATCH --output=logs/align_%A_%a.out

module load hisat2
module load samtools

SAMPLE=$(sed -n "${SLURM_ARRAY_TASK_ID}p" samples.txt)
REF="reference/genome_index"

echo "Aligning $SAMPLE"

# Align reads
hisat2 -p 8 -x $REF \
    -1 results/trimmed/${SAMPLE}_R1_paired.fastq.gz \
    -2 results/trimmed/${SAMPLE}_R2_paired.fastq.gz \
    | samtools sort -@ 8 -o results/aligned/${SAMPLE}.bam

# Index BAM file
samtools index results/aligned/${SAMPLE}.bam

echo "Alignment complete for $SAMPLE"
```

---

### Step 4: Count Reads

**Script: `04_count.sh`**
```bash
#!/bin/bash
#SBATCH --job-name=counts
#SBATCH --time=02:00:00
#SBATCH --mem=16G
#SBATCH --cpus-per-task=8
#SBATCH --output=logs/counts_%j.out

module load subread

# Get all BAM files
BAMS=$(ls results/aligned/*.bam | tr '\n' ' ')

echo "Counting reads for all samples"

featureCounts -T 8 -p -t exon -g gene_id \
    -a reference/annotation.gtf \
    -o results/counts/all_counts.txt \
    $BAMS

echo "Counting complete!"
```

Note: This job processes all samples together (not an array).

---

### Master Script to Submit Everything

**Script: `run_pipeline.sh`**
```bash
#!/bin/bash

echo "Submitting RNA-seq pipeline..."

# Submit jobs and capture job IDs
JOB1=$(sbatch --parsable 01_fastqc.sh)
echo "FastQC submitted: Job $JOB1"

JOB2=$(sbatch --parsable --dependency=afterok:$JOB1 02_trim.sh)
echo "Trimming submitted: Job $JOB2 (waits for FastQC)"

JOB3=$(sbatch --parsable --dependency=afterok:$JOB2 03_align.sh)
echo "Alignment submitted: Job $JOB3 (waits for trimming)"

JOB4=$(sbatch --parsable --dependency=afterok:$JOB3 04_count.sh)
echo "Counting submitted: Job $JOB4 (waits for alignment)"

echo "Pipeline submitted! Use 'squeue -u $USER' to monitor."
```

**What's happening here:**
- `--dependency=afterok:$JOB1` means "don't start until Job 1 finishes successfully"
- The entire pipeline runs automatically
- Each step waits for the previous one
- If any step fails, the pipeline stops

---

## Tips for Success

### 1. Start Small, Scale Up
- Test on 1-2 samples first
- Make sure everything works
- Then scale to all samples

### 2. Check Your Work at Each Step
```bash
# After FastQC
ls results/qc/*.html | wc -l          # Should match sample count

# After trimming
ls results/trimmed/*_paired.fastq.gz | wc -l  # Should be 2x sample count

# After alignment
ls results/aligned/*.bam | wc -l       # Should match sample count

# Check alignment rates
samtools flagstat results/aligned/sample_001.bam
```

### 3. Use Version Control for Scripts
Consider learning Git to track changes to your scripts. Your future self will thank you!

### 4. Keep a Lab Notebook
Document what you did, when, and why. A simple text file works:
```
2026-01-12: Started RNA-seq analysis
- 20 samples from treatment vs control
- Reference: GRCh38
- Ran FastQC - all samples look good
- Trimming parameters: MINLEN:36, SLIDINGWINDOW:4:15

2026-01-13: Completed alignment
- Average alignment rate: 87%
- Sample 15 had lower rate (72%) - checking with wet lab
```

---

## Common Issues and Solutions

### Issue: "Command not found"
**Solution:** You forgot to load the module
```bash
module load bwa  # Load the software first!
```

### Issue: Job killed for exceeding memory
**Solution:** Request more memory
```bash
#SBATCH --mem=64G  # Instead of 32G
```

### Issue: Job still pending after hours
**Solution:** You might be requesting too many resources. Check available resources:
```bash
sinfo  # See what's available
```

### Issue: Can't find output files
**Solution:** Check if job actually ran successfully
```bash
tail logs/my_job_12345.out  # Check the log file
```

### Issue: Wrong results
**Solution:** Check your input files and commands
```bash
# Verify input file
head raw_data/sample.fastq

# Run command manually on one sample to test
bwa mem reference.fasta sample_R1.fastq sample_R2.fastq > test.sam
```

---

## Resources for Continued Learning

### Online Tutorials
- Software Carpentry (shell and HPC basics)
- SLURM documentation (for your specific cluster)
- Bioinformatics tool manuals (read them!)

### Practice
- Start with small datasets
- Reproduce published analyses
- Join bioinformatics communities online

### Getting Help
- Your institution's HPC support team
- Bioinformatics Stack Exchange
- Tool-specific forums and mailing lists
- Your colleagues!

---

## Final Thoughts

You're learning a new language and a new way of thinking. It will feel awkward at first—that's normal! Every expert bioinformatician started exactly where you are now.

**Remember:**
- **Start simple:** Master basic commands before complex pipelines
- **Document everything:** Your future self will thank you
- **Ask for help:** Everyone gets stuck sometimes
- **Practice regularly:** Skills fade without use
- **Be patient with yourself:** This is a journey, not a destination

The skills you're learning will transform your research. You're no longer limited by the scale of data you can analyze manually. You can ask bigger questions, analyze more samples, and do science that wasn't possible before.

Welcome to bioinformatics. Welcome to the future of biological research. Now let's get started! 🧬💻🚀

---

## Quick Reference Card

### Essential Shell Commands
```bash
pwd                  # Where am I?
ls                   # What's here?
cd directory         # Go to directory
cd ..                # Go up one level
mkdir name           # Make directory
cp source dest       # Copy file
mv source dest       # Move/rename file
rm file              # Delete file (careful!)
cat file             # Show file
head file            # Show first lines
tail file            # Show last lines
less file            # Browse file (q to quit)
grep pattern file    # Search for pattern
wc -l file           # Count lines
```

### SLURM Commands
```bash
sbatch script.sh              # Submit job
squeue -u $USER              # Check your jobs
scancel jobid                # Cancel job
sinfo                        # Check available resources
seff jobid                   #
