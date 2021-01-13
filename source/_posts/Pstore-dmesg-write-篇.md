---
title: Pstore dmesg write 篇
tags:
  - Linux Pstore
categories:
  - Linux
description: 
date: 2021-01-08 08:40:47
---

1. 在需要dump kmsg的情景，如 reboot、oops和panic，调用kmsg_dumper。 kmsg_dumper通过遍历，将数据发给每个dumper。

 <!-- more -->

```c
void kmsg_dump(enum kmsg_dump_reason reason)
{
	struct kmsg_dumper *dumper;
        rcu_read_lock();
	list_for_each_entry_rcu(dumper, &dump_list, list) {
                dumper->dump(dumper, reason);
        } 
        rcu_read_unlock();
}
```

2.  pstore 的 dumper函数

   ```c
   static void pstore_dump(struct kmsg_dumper * dumper,
   			enum kmsg_dump_reason reason)
   {
   	/* 获取 dump 原因 */
   	why = get_reason_str(reason);
   	
   	oopscount++;
   	while (total < kmsg_bytes) {
   		
   		if (big_oops_buf && is_lock) {
   			/* 进行压缩 */
   			dst = big_oops_buf;
   			/* 获取数据 */
   			if (!kmsg_dump_get_buffer(dumper, true, dst + hsize,
   								size, &len))
   				break;
   				
   			/* 压缩数据 */
   			zipped_len = pstore_compress(dst, psinfo->buf,
   						hsize + len, psinfo->bufsize);
   						
   			if (zipped_len > 0) {
   				
   			} else {
   				/* 压缩不成功 */
   				compressed = false;
   				total_len = copy_kmsg_to_buffer(hsize, len);
   			}
   		} else {
   			/* 非压缩 */
   			dst = psinfo->buf;
   			hsize = sprintf(dst, "%s#%d Partu%\n", why, oopscount,
   									part);
   			size = psinfo->bufsize - hsize;
   			dst += hsize;
   			
   			/* 获取数据 */
   			if (!kmsg_dump_get_buffer(dumper, true, dst,
   								size, &len))
   				break;
   			
   			compressed = false;
   			total_len = hsize + len;
   		}
   		
   		/* 写入Pstore前段，如ramoops */
   		ret = psinfo->write(PSTORE_TYPE_DMESG, reason, &id, part,
   					oopscount, compressed, total_len, psinfo);
   		if (ret == 0 && reason == KMSG_DUMP_OOPS && pstore_is_mounted())
   			pstore_new_entry = 1; // for timer
   		
   		total += total_len;
   		part++;
   	}
   }
   ```

   

3. write 写入存储区

   ```c
   static int notrace ramoops_pstore_write_buf(enum pstore_type_id,
   						enum kmsg_dump_reason reason,
   						u64 * id, unsigned int part,
   						const char * buf,
   						bool compressed, size_t size,
   						struct pstore_info * psi)
   {
   	struct ramoops_context * cxt = psi->data;
   	struct persistent_ram_zone * prz;
   	size_t hlen;
   	
   	/* 写入 console、ftrace、pmsg 到 pstore */
   	if (type == PSTORE_TYPE_CONSOLE) {
   		persistent_ram_write(cxt->cprz, buf, size);
   	} else if (type == PSTORE_TYPE_FTRACE) {
   		
   	} else if (type == PSTORE_TYPE_PMSG) {
   		
   	}
   	
   	
   	/* 写入DMESG */
   	if (type != PSTORE_TYPE_DMESG)
   		return -EINVAL;
   	prz = cxt->przs[cxt->dump_write_cnt];
   	
   	hlen = ramoops_write_kmsg_hdr(prz, compressed);
   	if (size + hlen > prz->buffer_size)
   		size = prz->buffer_size - hlen;
   	persistent_ram_write(prz, buf, size);
   	
   	cxt->dump_write_cnt = (cxt->dump_write_cnt + 1) % cxt->max_dump_cnt;
   	
   	return 0;
   }
   ```

   ```c
   int notrace persistent_ram_write(struct persistent_ram_zone * prz,
   	const void * s, unsigned int count)
   {
   	int rem;
   	int c = count;
   	size_t start;
   	
   	/* 计算需要的大小 */
   	buffer_size_add(prz, c);
   	
   	/* 获取本次存储开始的位置 */
   	start = buffer_start_add(prz, c);
   	
   	/* 计算从当前位置可以存储的大小 */
   	rem = prz->buffer_size - start;
   	if (unlikely(rem < c)) {
   		/* 如果无法完全存储则覆盖之前的存储set start = 0 */
   		persistent_ram_update(prz, s, start, rem);
   		s += rem;
   		c -= rem;
   		start = 0;
   	}
   	
   	/* 存储数据 */
   	persistent_ram_update(prz, s, start, c);
    
   	/* 更新头部校验码 ECC */
   	persistent_ram_update_header_ecc(prz);
   }
   ```

   ```c
   static void notrace persistent_ram_update(struct persistent_ram_zone * prz,
   	const void * s, unsigned int start, unsigned int count)
   {
   	struct persistent_ram_buffer * buffer = prz->buffer;
   	memcpy_toio(buffer->data + start, s, count);
   	persistent_ram_update_ecc(prz, start, count);
   }
   ```

   

