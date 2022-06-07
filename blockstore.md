![image-20210810145342117](F:\mengzy\筆記\Untitled.assets\image-20210810145342117.png)

```
    "flatfs": {
      Description: `Configures the node to use the flatfs datastore.

This is the most battle-tested and reliable datastore, but it's significantly
slower than the badger datastore. You should use this datastore if:

* You need a very simple and very reliable datastore and you trust your
  filesystem. This datastore stores each block as a separate file in the
  underlying filesystem so it's unlikely to loose data unless there's an issue
  with the underlying file system.
* You need to run garbage collection on a small (<= 10GiB) datastore. The
  default datastore, badger, can leave several gigabytes of data behind when
  garbage collecting.
* You're concerned about memory usage. In its default configuration, badger can
  use up to several gigabytes of memory.

This profile may only be applied when first initializing the node.
```



```
"badgerds": {
      Description: `Configures the node to use the badger datastore.

This is the fastest datastore. Use this datastore if performance, especially
when adding many gigabytes of files, is critical. However:

* This datastore will not properly reclaim space when your datastore is
  smaller than several gigabytes. If you run IPFS with '--enable-gc' (you have
  enabled block-level garbage collection), you plan on storing very little data in
  your IPFS node, and disk usage is more critical than performance, consider using
  flatfs.
* This datastore uses up to several gigabytes of memory. 

This profile may only be applied when first initializing the node.`
```