# C语言内存函数详解

## 目录
1. memcpy 使用和模拟实现
2. memmove 使用和模拟实现
3. memset 函数的使用
4. memcmp 函数的使用

---

## 1. memcpy 使用和模拟实现

### 函数原型
```c
void *memcpy(void *destination, const void *source, size_t num);
```

### 功能说明
- 从 source 位置开始向后复制 num 个字节的数据到 destination 指向的内存位置
- 遇到 '\0' 时不会停止复制
- 如果 source 和 destination 有任何重叠，复制结果是未定义的

### 使用示例
```c
#include <stdio.h>
#include <string.h>

int main()
{
    int src[] = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
    int dest[10] = {0};
    
    memcpy(dest, src, 5 * sizeof(int));
    
    for (int i = 0; i < 10; i++)
    {
        printf("%d ", dest[i]);
    }
    printf("\n");
    
    return 0;
}
```

### 模拟实现
```c
void *my_memcpy(void *dest, const void *src, size_t n)
{
    void *ret = dest;
    assert(dest != NULL);
    assert(src != NULL);
    
    // 逐字节复制
    while (n--)
    {
        *(char *)dest = *(char *)src;
        dest = (char *)dest + 1;
        src = (char *)src + 1;
    }
    
    return ret;
}
```

---

## 2. memmove 使用和模拟实现

### 函数原型
```c
void *memmove(void *destination, const void *source, size_t num);
```

### 功能说明
- 与 memcpy 的区别在于 memmove 可以处理源内存块和目标内存块重叠的情况
- 当源空间和目标空间出现重叠时，应使用 memmove 函数

### 使用示例
```c
#include <stdio.h>
#include <string.h>

int main()
{
    int data[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // 内存重叠的情况
    memmove(data + 2, data, 5 * sizeof(int));
    
    for (int i = 0; i < 10; i++)
    {
        printf("%d ", data[i]);
    }
    printf("\n");
    
    return 0;
}
```

输出结果：
```
1 2 1 2 3 4 5 8 9 10
```

### 模拟实现
```c
void *my_memmove(void *dest, const void *src, size_t n)
{
    void *ret = dest;
    
    // 判断是否重叠以及复制方向
    if (dest <= src || (char *)dest >= ((char *)src + n))
    {
        // 无重叠或dest在src前面，从前向后复制
        while (n--)
        {
            *(char *)dest = *(char *)src;
            dest = (char *)dest + 1;
            src = (char *)src + 1;
        }
    }
    else
    {
        // 有重叠且dest在src后面，从后向前复制
        dest = (char *)dest + n - 1;
        src = (char *)src + n - 1;
        
        while (n--)
        {
            *(char *)dest = *(char *)src;
            dest = (char *)dest - 1;
            src = (char *)src - 1;
        }
    }
    
    return ret;
}
```

---

## 3. memset 函数的使用

### 函数原型
```c
void *memset(void *ptr, int value, size_t num);
```

### 功能说明
- 用来设置内存，将内存中的值以字节为单位设置成指定内容
- value 参数会被转换为 unsigned char 类型

### 使用示例
```c
#include <stdio.h>
#include <string.h>

int main()
{
    char text[] = "hello world";
    
    memset(text, 'A', 5);
    printf("%s\n", text);
    
    // 初始化数组
    int numbers[10];
    memset(numbers, 0, sizeof(numbers));
    
    return 0;
}
```

输出结果：
```
AAAAAworld
```

---

## 4. memcmp 函数的使用

### 函数原型
```c
int memcmp(const void *ptr1, const void *ptr2, size_t num);
```

### 功能说明
- 比较从 ptr1 和 ptr2 指针指向的位置开始，向后的 num 个字节
- 返回值：
  - 返回值 < 0：ptr1 小于 ptr2
  - 返回值 = 0：ptr1 等于 ptr2
  - 返回值 > 0：ptr1 大于 ptr2

### 使用示例
```c
#include <stdio.h>
#include <string.h>

int main()
{
    char str1[] = "Program123";
    char str2[] = "Program456";
    int result;
    
    result = memcmp(str1, str2, sizeof(str1));
    
    if (result > 0)
        printf("'%s' 大于 '%s'\n", str1, str2);
    else if (result < 0)
        printf("'%s' 小于 '%s'\n", str1, str2);
    else
        printf("'%s' 等于 '%s'\n", str1, str2);
    
    return 0;
}
```

---

## 重点总结

1. **memcpy**：用于非重叠内存的快速复制
2. **memmove**：可处理重叠内存的安全复制
3. **memset**：按字节设置内存内容
4. **memcmp**：按字节比较内存内容

**注意事项**：
- 所有函数都是按字节操作的
- 使用时要确保目标内存有足够的空间
- 内存重叠时必须使用 memmove 而不是 memcpy