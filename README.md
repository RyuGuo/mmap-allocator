# mmap allocator

在 mmap 内存空间中的内存分配器

依赖仓库: `https://github.com/RyuGuo/usable_lib.git`

## 示例

```c++
#include <mm_allocator.h>
#include <iostream>

using namespace mm_allocator;

void mdrain() {}

int main(int argc, char **argv) {
  // mmap 打开文件
  const size_t p_map_size = 1UL << 30;
  fd = open(argv[1], O_CREAT | O_RDWR | O_TRUNC, 00777);
  void *p_map = mmap(NULL, p_map_size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);

  // 初始化环境
  env_init(p_map, p_map_size, msync, mdrain);

  // 申请空间
  mm_ptr<int> p1 = mm_cast<int>(mm_alloc(sizeof(int)));
  mm_ptr<double> p2 = mm_cast<double>(mm_alloc(sizeof(double)));

  // 判断是否成功
  if (p1 == mm_nullptr || !p2) {
    std::cout << "error" << std::endl;
    exit(-1);
  }

  // 赋值
  *p1 = 0;
  *p2 = 3.14;

  // 释放空间
  mm_free(p1);
  mm_free(p2);

  // 关闭 mmap 文件
  close(fd);
  munmap(p_map, p_map_size);

  // 释放环境
  env_release();
  return 0;
}

```