1. Quick Start
```C
/**
 * \file main.c
 * \date 2023.10.15
 * \author 3hex
 * \brief 这个工程介绍了绝大部的C语言语法
 * \version v1.0.0
*/
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

/** Debug模式，启用assert */
#define DEBUG_CUS
//#define ASSERT_ABORT

#ifdef DEBUG_CUS
#define assert_cus(exp) (void)((!!(exp)) || (_assert_cus(#exp, __FILE__, __LINE__),0))
#else
#define assert_cus(exp) ((void)0)
#endif

/** 打印中的间隔字符 */
#define DEC "--------------------------------------"
/** 输入数据最大数量 */
#define MAX_N (10) 
/** 字符串是否相等 */
#define STR_EQU(x, y)  (strcmp(x, y) == 0 ? 1 : 0)

/** 定义uint16为unsigned int*/
typedef unsigned int uint16;
/** 定义数据结构体 */
typedef struct _data
{   
    uint16 data[MAX_N];
    uint16 res;
    uint16 data_len;
    void (* data_func)(struct _data *data);
}Data, *pData;

/** 定义函数返回值 */
typedef enum _RET
{
    RET_OK=0, RET_ERROR_1, RET_ERROR_2
}RET;

/** 全局变量 */
char file_name[10] = "";
char calc_mode[10] = "";
char param_str[][3] = {"-f", "-m"};
char mode_param_str[][5] = {"xor", "sum"};

/** 函数声明 */
uint16 param_handle(const int *p_argc, const char **p_argv);
void _assert_cus(const char *exp_message, const char *file_name, const int line_num);
uint16 load_data_with_convert(const char *data_file_name, pData data);
void print_data_struct(const Data *data);
uint16 data_func_selector(const char *func_mode, pData data);
void data_xor(pData data);
void data_sum(pData data);

/***
 * \brief 读取文件名参数(-f指定)，数据处理参数(-m指定)，实现对数据的处理
 * \param argc 输入参数个数
 * \param argv 输出参数字符串
 * \details 例如：.\main.exe  -m sum -f data.txt
 * \return 
 *      \retval RET_OK
*/
int main(int argc, char *argv[])
/// 定义argv表示接收参数的个数,argc表示参数字符串。
/// 注：argc[0]是<当前可执行文件绝对路径>
{
    int i = 0;
    uint16 res = 0;
    Data loc_data;

    /** 参数解析 */
    assert_cus(param_handle(&argc, (const char **)argv) == RET_OK);
    puts(file_name);
    puts(calc_mode);

    /** 载入数据 */
    load_data_with_convert(file_name, &loc_data);
    print_data_struct(&loc_data);

    /** 数据处理选择 */
    data_func_selector(calc_mode, &loc_data);
    print_data_struct(&loc_data);

    /** 数据处理 */
    loc_data.data_func(&loc_data);
    print_data_struct(&loc_data);

    return RET_OK;
}

/***
 * \brief 输入的参数识别
 * \param argc 输入参数个数
 * \param argv 输出参数字符串
 * \return
 *      \retval RET_OK 识别成功
 *      \retval RET_ERROR_1 未定义的参数选项
*/
uint16 param_handle(const int *p_argc, const char **p_argv)
{
    int i = 0;
    /** 异常判断 */
    assert_cus(*p_argc==5);

    /** 参数解析 */
    for (i = 1;i < *p_argc;i++)
    {
        if(STR_EQU(p_argv[i], param_str[0])) /// -f
        {
            i++;
            strcpy(file_name, p_argv[i]);
            continue;
        }
        else if(STR_EQU(p_argv[i], param_str[1])) /// -m
        {
            i++;
            strcpy(calc_mode, p_argv[i]);
            continue;
        }
        else
        {
            return RET_ERROR_1;
        }
    }
    return RET_OK;
}

/***
 * \brief 读取数据文件，并载入到data数据结构中
 * \param data_file_name 文件名
 * \param data data数据
 * \return
 *      \retval RET_OK 载入成功
*/
uint16 load_data_with_convert(const char *data_file_name, pData data)
{
    FILE *fp = fopen(data_file_name, "r");
    int i = 0;
    char buf[512] = "";
    uint16 data_tmp = 0; 
    assert_cus(fp!=NULL);
    while(1)
    {
        fgets(buf, 512, fp);
        data_tmp = strtol(buf, NULL, 16);
        data->data[i++] = data_tmp;
        if(feof(fp)) break; /// feof：光标后还有内容，返回0；没有内容了，返回非0
    }

    data->data_len = i;
    data->res = 0;
    data->data_func = NULL;

    return RET_OK;
}

/**
 * \brief 打印data的数据结构
 * \param data data数据
 * \return void 
*/
void print_data_struct(const Data *data)
{
    int i = 0;
    puts(DEC);
    printf("data_len: %d\n", data->data_len);
    printf("data:");
    for (;i<data->data_len;i++)
    {
        printf("%d\t", data->data[i]);
    }
    printf("\nres:%d\n", data->res);
    printf("data_func:%p\n", data->data_func);
    puts(DEC);

}

/**
 * \brief data数据结构选择处理函数
 * \param func_mode 数据处理方式字符串，例如xor\sum
 * \param data data数据
 * \return 
 *      \retval RET_OK 处理完成
 *      \retval RET_ERROR_1 未定义的数据处理方式
*/
uint16 data_func_selector(const char *func_mode, pData data)
{
    if(STR_EQU(mode_param_str[0], func_mode))
    {
        data->data_func = &data_xor;
    }
    else if(STR_EQU(mode_param_str[1], func_mode))
    {
        data->data_func = &data_sum;
    }
    else
    {
        return RET_ERROR_1;        
    }
    return RET_OK;
}

/**
 * \brief 数据处理方式，异或处理
 * \param data data数据
 * \return void
*/
void data_xor(pData data)
{
    int i = 0;
    for (;i<data->data_len;i++)
    {
       data->res ^= data->data[i];
    }
}


/**
 * \brief 数据处理方式，累加求和处理
 * \param data data数据
 * \return void
*/
void data_sum(pData data)
{
    int i = 0;
    for (;i<data->data_len;i++)
    {
       data->res += data->data[i];
    }
}

void _assert_cus(const char *exp_message, const char *file_name, const int line_num)
{
	fflush(stdout);
	fprintf(stderr, "\n[Assert error][F:%s-L:%d]Assertion '%s' failed \n", file_name, line_num, exp_message);
	fflush(stderr);
    
#ifdef ASSERT_ABORT
	abort();
#endif
}
```
2. C基础
   1. 预处理
      1. #include
      2. #define
      3. #ifdef/#ifndef ... #else ... #endif
      4. #if ... #elif ... #endif
      5. 预定义宏
      6. #line && #error
      7. #pragma message/once/pack
   2. assert（断言）
   3. 指针
      1. 变量指针
      2. 函数指针
      3. 二级/多级指针
   4. 变量声明
      1. extern
      2. static
      3. auto
      4. register
      5. const
      6. volatile
   5. 高级结构
      1. 结构体
      2. 枚举
      3. 共用体