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

### One-shot Solution

``` hox import reads --from-accession=ERR3239334 --id-only | xargs -I {} hox create aln --name=demo_aln_2 --reads={} --assembly=$(hox search asm --prefix hox-lab/t2t --limit 1 --id-only)```


## Tag and Search Resources

HoX allows resources to be tagged with key value pairs. These key value pairs are indexed and 


First lets look at the default tags that exist on our newly create alignment resource. We can see that there are internal tags (hox.*) that show the path and path length of the resource. This tells us that in the GUI this resource would be in the "root" directory of its project.

``` hox tag reads 5bf83063```

```
{
        "hox.path.1": "demo_aln",
        "hox.path.len": 1
}
```

Now lets add a tag to the resource

``` hox tag reads 5bf83063 --set foo=bar```  (this can be removed with ``` hox tag reads 5bf83063 --unset foo```)
```
{
        "foo": "bar",
        "hox.path.1": "demo_aln",
        "hox.path.len": 1
}
```

We can use tags as a powerful search tool...

``` hox search aln --tags-json='{"foo":"bar"}'```
```
ID       NAME     STATUS  SIZE CREATED_AT     UPDATED_AT
5bf83063 demo_aln pending 0B   36 minutes ago 36 minutes ago
```

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
