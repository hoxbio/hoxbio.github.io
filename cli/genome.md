# HoX CLI Demo - Genomics

1. [Import and Create Resources](#import-and-create-resources)
2. [Tag and Search Resources](#tag-and-search-resources)
3. [Export Resources](#export-resources)

## Import and Create Resources

In this demo we will be importing a [demo dataset](https://trace.ncbi.nlm.nih.gov/Traces/index.html?view=run_browser&acc=ERR3239334&display=metadata) from the SRA and aligning it against chm13v2. HoX provides flexibility in how you can queue jobs on resources. Even before the job is complete, the resource is availible to have subsequent jobs scheduled (i.e. create an alignment from reads that are still importing), tag the resource with key value pairs, or create comments. Should you desire a one line solution, using the ```--id-only``` flag and ```xargs``` allows you to represent an entire Workflow within the command line.

This same process can be run through the Worflows tab in the GUI.

1. [Import Reads](#import)
2. [Create Alignment](#create-alignment)

### Import

Import a FASTQ-formatted readgroup 

``` hox import reads --from-accession=ERR3239334 ```

```
ID       NAME                           STATUS  SIZE CREATED_AT             UPDATED_AT
310b6134 ncbi-sra/ERR3239334/ERR3239334 pending 0B   Less than a second ago Less than a second ago
```

or

``` hox import reads --name=foo --r1=bar_r1.fq.gz --r2=bar_r2.fq.gz ```

### Create Alignment

``` hox get asm ```
```
ID       NAME                           SUBTYPE STATUS    SIZE     CREATED_AT  UPDATED_AT
7c3f60a3 share/grcm39-cellranger-2024-A genome  completed 6.285GiB 3 weeks ago 3 weeks ago
a159b7ad hox-lab/grch38-20250602        genome  completed 7.104GiB 3 weeks ago 3 weeks ago
37085abb hox-lab/t2t-chm13v2-20250602   genome  completed 7.282GiB 3 weeks ago 3 weeks ago
```


``` hox create aln --name=demo_aln --reads=310b6134 --assembly=37085abb```

```
ID       NAME     STATUS  SIZE CREATED_AT             UPDATED_AT
5bf83063 demo_aln pending 0B   Less than a second ago Less than a second ago
```

> [!TIP]
> Variants are called in real time in the GUI viewer, however, should you desire a VCF file, you can create a ```variants``` or ```genotypes``` resource and run an export job.

### One-shot Solution

``` hox import reads --from-accession=ERR3239334 --id-only | xargs -I {} hox create aln --name=demo_aln_2 --reads={} --assembly=$(hox search asm --prefix hox-lab/t2t --limit 1 --id-only)```
## Export Resources

We can create an export job which will package the resource into an industry standard format (in this case SAM). That will become downloadable once the job is complete.

``` hox export aln 5bf83063```
```
ID       STATUS  RESOURCE_ID EXPIRES_AT
2579282d pending 5bf83063    7 days from now
```
``` hox download ex 2579282d```

If you want a hands off solution that will start the job, wait for completion, then download to your machine, you can run the following:

``` hox export aln 5bf83063 --id-only | xargs hox download ex --wait ```
