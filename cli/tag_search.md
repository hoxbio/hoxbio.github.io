# HoX CLI Demo Tag and Search

## Tag and Search Resources

HoX allows resources to be tagged with key value pairs. These key value pairs are indexed and 


First lets look at the default tags that exist on a newly create alignment resource (resource ```5bf83063```). We can see that there are internal tags (hox.*) that show the path and path length of the resource. This tells us that in the GUI this resource would be in the "root" directory of its project.

``` hox tag reads 5bf83063```

```
{
        "hox.path.1": "demo_aln",
        "hox.path.len": 1
}
```

Now lets add a tag to the resource

``` hox tag reads 5bf83063 --set foo=bar```  (this can be removed with ``` hox tag reads 5bf83063} --unset foo```)
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
