---
title: Pstore dmesg read篇
tags:
  - Linux Pstore
categories:
  - Linux
description:
date: 2021-01-08 08:40:47
---

   当系统重启后，可以把数据从pstore中读出来，并创建文件节点，提供用户接口来获取文本数据。

  ```c
static int ramoops_probe(struct platform_device * pdev)
{
	/* 从pstore中取出数据 */
	err = ramoops_init_przs(dev, cxt, &paddr, dump_mem_sz);
	
	/* 写入文件节点 */
	err = pstore_register(&cxt->pstore);
}
  ```

 <!-- more -->

1. 读取数据

   ```c
   static int ramoops_init_przs(struct device * dev, struct ramoops_context cxt,
   				phys_addr_t * paddr, size_t dump_mem_sz)
   {
   	for (i = 0; i < cxt->max_dump_cnt; i++) {
   		cxt->przs[i] = persistent_ram_new(*paddr, cxt->record_size, 0,
   							&cxt->ecc_info,
   							cxt->memtype, 0);
   		*paddr += cxt->record_size;
   	}
   	return 0;
   }
    
   struct persistent_ram_zone * persistent_ram_new(phys_addr_t start, size_t size,
   			u32 sig, struct persistent_ram_ecc_info * ecc_info,
   			unsigned int memtype, u32 flags)
   {
   	struct persistent_ram_zone * prz;
   	
   	ret = persistent_ram_post_init(prz, sig, ecc_info);
   	return ERR_PTR(ret);
   }
    
   static int persistent_ram_post_init(struct persistent_ram_zone *prz, u32 sig,
   					struct persistent_ram_ecc_info * ecc_info)
   {
   	/* 通过魔术字判断是否有dump数据 */
   	sig ^= PERSISTENT_RAM_SIG;
   	
   	if (prz->buffer->sig == sig) {
   		persistent_ram_save_old(prz);
   	}
   	
   	prz->buffer->sig = sig;
   	
   	return 0;
   }
    
   void persistent_ram_save_old(struct persistent_ram_zone * prz)
   {
   	struct persistent_ram_buffer * buffer = prz->buffer;
   	size_t size  = buffer_size(prz);
   	size_t start = buffer_start(prz);
   	
   	prz->old_log_size = size;
   	/* 环形缓冲区，start是起点 */
   	memcpy_fromio(prz->old, &buffer->data[start], size - start);
   	memcpy_fromio(prz->old_log + size -start, &buffer->data[0], start);
   	
   }
   ```

   

2.  创建文件节点，提供用户接口

   ```c
   int pstore_register(struct pstore_info * psi)
   {
   	if (pstore_is_mounted())
   		pstore_get_records(0);
   }
    
   void pstore_get_records(int quiet)
   {
   	while ((size = psi->read(&id, &type, &count, &time, &buf, &compressed,
   				psi)) > 0) {
   		if (compressed && (type == PSTORE_TYPE_DMESG)) {
   			if (big_oops_buf)
   				/* 解压 */
   				unzipped_len = pstore_decompress(buf,
   							big_oops_buf, size,
   							big_oops_buf_sz);			
   		}
   		
   		/* 创建文件节点 */
   		rc = pstore_mkfile(type, psi->name, id, count, buf,
   					compressed, (size_t)size, time, psi);
   					
   	}
   }
   ```

   

