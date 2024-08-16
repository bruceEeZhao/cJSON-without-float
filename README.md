# 简介
为了在不支持浮点运算的平台上使用cJOSN，进行了去浮点操作的修改，

添加了 `DISSABLE_FLOAT` 开关，该开关打开的情况下，会禁止浮点相关的操作

在开关 `DISSABLE_FLOAT` 打开的情况下：
- 部分函数直接不编译，例如 `cJSON_CreateFloatArray` 函数
- 部分函数重新编写，例如 `parse_number` 函数

# 使用方法

编写测试函数： `vim test_dissable_float.c`

```
#include<stdio.h>
#include"cJSON.h"

int main() 
{
    
    char msg[] = "{\"num\":123, \"name\":\"xxx\", \"array\":[11,22,33]}";

    cJSON* cjson_obj = cJSON_Parse(msg);
    if(cjson_obj == NULL)
    {
        printf("parse fail.\n");
        return -1;
    }

    int num = cJSON_GetObjectItem(cjson_obj, "num")->valueint;
    printf("num is %d\n", num);

    char *name = cJSON_GetObjectItem(cjson_obj, "name")->valuestring;
    if (name == NULL) {
        printf("there is no name in json\n");
        return -1;
    } else {
        printf("name is %s \n", name);
    }

    cJSON *array = cJSON_GetObjectItem(cjson_obj, "array");
    int array_size = cJSON_GetArraySize(array);
    for(int cnt = 0;cnt < array_size;cnt++)
    {
        cJSON *array_item = cJSON_GetArrayItem(array, cnt);
        if(array_item == NULL)
        {	
            continue;	
        }

        printf("num %d in array is %d \n", cnt, array_item->valueint);
    }


    return 0;
}
```

编译：

```bash
$ gcc -DDISSABLE_FLOAT=1 test_dissable_float.c cJSON.c -o test_dissable_float
```

结果：

```bash
$ ./test_dissable_float 
num is 123
name is xxx 
num 0 in array is 11 
num 1 in array is 22 
num 2 in array is 33
```