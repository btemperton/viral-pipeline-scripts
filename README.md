# viral-pipeline-scripts

This is a repository for scripts to make the viral identification and annotation pipeline described [here]() more efficient.

## `dram-v-wrapper.py`
`DRAM-v` is an amazing piece of software, but it's SLOW. It seems to process viral contigs one at a time, which is fine if you only have a few, but unwieldy if you have many, as each one tends to take several minutes to complete. While you can specify the number of threads assigned to `DRAM-v`, it doesn't really do much to speed things up.

This simple script just splits the `fasta` file into numerous subsamples and then runs the jobs in parallel. It then combines the output of each subprocess (`annotations.tsv`) into a single dataframe and outputs that so you can continue the pipeline.

You can run it as follows:
```
dram-v-wrapper.py \
--in_fasta_file test10.fa \
--in_tab_file viral-affi-contigs-for-dramv.tab \
--outfile test.tsv \
--job_number 10
```

This will launch up to `10` jobs in parallel (each assigned 4 threads by default but can be set with the `--threads_per_process` parameter, so scale accordingly and use `htop` to monitor your CPU usage). The output of each subprocess will be combined into `test.tsv`.

As a test, running 10 putative viral sequences as a single process took XXX on a 64 core ubuntu machine with 128Gb of RAM

This script is rough and ready (basically because I needed it to work ASAP for some data I'm analysing), so feel free to adapt and use pull requests to improve it!
