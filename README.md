# viral-pipeline-scripts

This is a repository for scripts to make the viral identification and annotation pipeline described [here](https://www.protocols.io/view/viral-sequence-identification-sop-with-virsorter2-bwm5pc86) more efficient.

## `dram-v-wrapper.py`
`DRAM-v` is an amazing piece of software, but it's SLOW. It seems to process viral contigs one at a time, which is fine if you only have a few, but unwieldy if you have many, as each one tends to take several minutes to complete. While you can specify the number of threads assigned to `DRAM-v`, it doesn't really do much to speed things up.

This simple script just splits the `fasta` file into numerous subsamples and then runs the jobs in parallel. It then combines the output of each subprocess (`annotations.tsv`) into a single dataframe and outputs that so you can continue the pipeline.

It requires `python3`, `pandas` and `shutil`

You can run it as follows:
```
python dram-v-wrapper.py \
--in_fasta_file test10.fa \
--in_tab_file viral-affi-contigs-for-dramv.tab \
--outfile test.tsv \
--job_number 10
```

This will launch up to `10` jobs in parallel (each assigned 16 threads by default but can be set with the `--threads_per_process` parameter, so scale accordingly and use `htop` to monitor your CPU usage. If there are more than 10 jobs, these will be run as room becomes available on the queue.

`DRAM-v` really doesn't use very much multiprocessing so the sum can probably exceed the total number of cores). The output of each subprocess will be combined into `test.tsv`.

As a test, running 10 putative viral sequences as a single process took ~16 min and had a peak memory usage of ~17 Gb of RAM on a 64 core ubuntu machine with 128Gb of RAM. Running all 10 contigs in parallel took 2min 35s with a similar memory peak requirement.

This script is rough and ready (basically because I needed it to work ASAP for some data I'm analysing), so feel free to adapt and use pull requests to improve it!
