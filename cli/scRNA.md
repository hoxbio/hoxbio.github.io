# HoX CLI Demo - scRNA

1. [Import and Create Resources](#import-and-create-resources)
<!-- 2. [Tag and Search Resources](#tag-and-search-resources)
3. [Export Resources](#export-resources) -->

## Import and Create Resources


1. [Import Reads](#import)
2. [Create Alignment](#create-psuedoalignment)
3. [Create Gene Matrix](#create-gene-matrix)

### Import

Import a FASTQ-formatted readgroup 

``` hox import reads --from-accession=SRR7611046 ```

```
ID       NAME                           STATUS  SIZE CREATED_AT             UPDATED_AT
2cf8d4e5 ncbi-sra/SRR7611046/SRR7611046 pending 0B   Less than a second ago Less than a second ago
```

or

``` hox import reads --name=foo --r1=bar_r1.fq.gz --r2=bar_r2.fq.gz ```

### Create Psuedoalignment

``` hox get asm ```
```
ID       NAME                           SUBTYPE STATUS    SIZE     CREATED_AT  UPDATED_AT
7c3f60a3 share/grcm39-cellranger-2024-A genome  completed 6.285GiB 3 weeks ago 3 weeks ago
a159b7ad hox-lab/grch38-20250602        genome  completed 7.104GiB 3 weeks ago 3 weeks ago
37085abb hox-lab/t2t-chm13v2-20250602   genome  completed 7.282GiB 3 weeks ago 3 weeks ago
```
``` hox get ft```
```
ID       NAME                              STATUS    SIZE     CREATED_AT  UPDATED_AT
1db4b769 share/grcm39-cellranger-2024-A-FT completed 35.65MiB 3 weeks ago 3 weeks ago
fd4f961e hox-lab/grch38-cellranger-2020-A  completed 53.91MiB 3 weeks ago 3 weeks ago
6e3f38d4 hox-lab/grch38-cellranger-2024-A  completed 63.41MiB 3 weeks ago 3 weeks ago
```


``` hox create pal --type=scRNA --name=demo_pal --reads=2cf8d4e5 --assembly=7c3f60a3 --geometry=10x-chromium-3p-v3 --features=1db4b769 ```

```
ID       NAME     STATUS  SUBTYPE SIZE CREATED_AT             UPDATED_AT
68b82e4f demo_pal pending scRNA   0B   Less than a second ago Less than a second ago
```

#### One-shot Solution

``` 
hox import reads --from-accession=SRR7611046 --id-only \
        | xargs -I {} hox create pal --name=demo_pal --type=scRNA --reads={} \
        --assembly=$(hox search asm --prefix share/grcm39 --limit 1 --id-only) \
        --features=$(hox search ft --prefix share/grcm39 --limit 1 --id-only) \
```

### Create Gene Matrix

``` hox create gex --pal-id=68b82e4f --cell-filter=expect=4500 ```
```
ID       NAME              STATUS  SIZE CREATED_AT             UPDATED_AT
6d208e10 demo_pal-2BP3IKFK pending 0B   Less than a second ago Less than a second ago
```
