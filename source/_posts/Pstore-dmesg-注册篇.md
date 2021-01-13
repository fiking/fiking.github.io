---
title: Pstore dmesg 注册篇
tags:
  - Linux Pstore
categories:
  - Linux
description: 个人博客搭建笔记。
date: 2021-01-08 08:40:47
---

### 注册pstore_info，提供pstore存储区域和操作函数；

 <!-- more -->

````c
static int ramoops_probe(struct platform_device *pdev)
{
	struct ramoops_context * cxt = &oops_cxt;
	
	/*
	 * Only a single ramoops area allowed at a time
	 */
	if (cxt->max_dump_cnt)
		goto fail_out;
	
	cxt->pstore.data = cxt;
 
	err = pstore_register(&cxt-pstore);
}
 
static struct ramoops_context oops_cxt = {
	.pstore = {
		.owner  = THIS_MODULE,
		.name   = "ramoops",
		.open   = ramoops_pstore_open,
		.read   = ramoops_pstore_read,
		.write_buf = ramoops_pstore_write_buf,
		.erase  = ramoops_pstore_erase,
	};
};
````

### 注册kmsg dumper；

```c
int pstore_register(struct pstore_info * psi)
{
	/* 用 write_buf 实现 write */
	if (!psi->write)
		psi->write = pstore_write_compat;
	
	/* only one psinfo */
	psinfo = psi;
	
	allocate_buf_for_compression();
	
	/* 注册kernel dump */
	pstore_register_kmsg();
}
 
static void allocate_buf_for_compression(void)
{
	/* 用于压缩的log */
	big_oops_buf_sz = (psinfo->bufsize * 100) / cmpr;
	big_oops_buf = kmalloc(big_oops_buf_sz, GFP_KERNEL);
}
 
static void pstore_register_kmsg(void )
{
	kmsg_dump_register(&pstore_dumper);
}
 
static struct kmsg_dumper pstore_dumper = {
	.dump = pstore_dump,
}；
```



