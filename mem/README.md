kernel memory

https://lkml.org/lkml/2006/3/16/170 (Linus re insert_page vs remap_pfn_range)

mmap, ops->nopage, if NULL -> kernel allocates an empty page (try this)
nopage? alloc_page [get_free_page] -> vm_insert_page

PHYSICALLY contiguous memory
----------------------------

kmalloc(size, flags):
    returns a virtual address; 
    memory is contiguous in physical memory
    GFP_KERNEL (process context; can sleep)
    GFP_ATOMIC (do not sleep)
    GFP_USER (memory for user space pages)
    ...

Requesting whole pages
    get_zeroed_page (clears it)
    __get_free_page (does not clear)
    __get_free_pages (multiple phyiscally contiguous, not cleared, pages)

VIRTUALLY contiguous memory
---------------------------

vmalloc(size)
    may sleep
    uses memory set aside for vmalloc (VMALLOC_START - VMALLOC_END)
      cat /proc/meminfo | grep VmallocTotal
      VmallocTotal:   34359738367 kB
    require the MMU; these addresses must be used _within_ the processor
    release using vfree

Mapping device memory into a process
    In response to mmap a device driver can use remap_pfn_range.
    This is capable of mapping a physical page range into the
    virtual memory of a process. It has a caveat: only certain
    page ranges can be so mapped. These include the "device 
    memory" above physical RAM (picture a video card framebuffer
    which appears at a high physical address bus but is above RAM).

Other
  vm_insert_page