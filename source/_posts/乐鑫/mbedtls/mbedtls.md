---
title: mbedtls
date: 2021-01-05 00:00:00
swiper: false
swiperImg: '/medias/5.jpg'
top: false
tags: 乐鑫
---

# 一、伪随机数生成器（ctr_drbg）的配置与使用

## 真随机数和伪随机数

### 区别

随机数在安全技术中通常被用于生成随机序列（eg. 秘钥），对于一个随机数的生成而言，是否真正的做到“随机”是最重要的。

真随机数通常来源于硬件随机数生成器，每次生成的随机数都是真正的随机数，但是因为物理因素，**生成的时间较慢**。比如STM32中提供的RNG硬件外设。

伪随机数通常来源于某个生成算法，每次生成的随机数虽然是随机的，但还是遵循生成算法的规则，优点是**生成速度较快**。比如C库中提供的rand函数，在相同的种子下，其生成的随机数序列相同，所以称之为伪随机数。

所以一般情况下，有一种比较巧妙的办法：将两者结合起来，**先使用真随机数生成种子，然后使用伪随机数生成算法**，这样既保证了生成速度，又保证了生成的序列是真正的随机数。

对应到 mbedtls 中，将产生真随机数的模块称为真随机数生成器（TRNG），将	产生伪随机数的模块称为伪随机数发生器（PRNG）（也叫做确定性随机数生成器，DRBG），其中伪随机数所使用的种子称为**熵源（熵池）**。

### 伪随机数生成算法

NIST SP 800-90A规范中描述了三种产生伪随机数的算法：

- Hash_DRBG：使用单向散列算法作为伪随机数生成的基础算法；
- HMAC_DRBG：使用消息认证码算法作为随机数生成的基础算法；
- CRT_DRBG：使用分组密码算法的计数器模式作为随机数生成的基础算法（重点）。

CRT_DRBG 可以理解为一个AES加密过程，加密结果为期望随机数序列。

## 自定义熵源接口

mbedtls中可以通过开启宏定义 **MBEDTLS_ENTROPY_HARDWARE_ALT** 来开启熵源接口，用户可以在熵源接口中实现自己的硬件获取真随机数代码。

### 开启宏定义

```c
// 定义此宏，以使mbed TLS使用您自己的硬件熵收集器实现。
// 函数原型必须为 int mbedtls_hardware_poll( void *data, unsigned char *output, size_t len, size_t *olen );
// 并接受NULL作为第一个参数
#define MBEDTLS_ENTROPY_HARDWARE_ALT
```

### 自定义实现mbedtls_hardware_poll函数

创建一个新文件用于存放我们编写的该函数实现，这里我创建文件`entropy_hardware_alt.c`。

实现时需要包含头文件`entropy_poll.h`，其中包含了 mbedtls_hardware_poll() 函数的原型定义：

```c
#if defined(MBEDTLS_ENTROPY_HARDWARE_ALT)
int mbedtls_hardware_poll( void *data, unsigned char *output, size_t len, size_t *olen );
#endif
```

这里以stm32为例

```c
#include "mbedtls/entropy_poll.h"
#ifdef MBEDTLS_ENTROPY_HARDWARE_ALT

#include "main.h"
#include "string.h"
#include "stm32l4xx_hal.h"
#include "mbedtls/entropy_poll.h"

extern RNG_HandleTypeDef hrng;

int mbedtls_hardware_poll( void *Data, unsigned char *Output, size_t Len, size_t *oLen )
{
    uint32_t index;
    uint32_t randomValue;
		
    for (index = 0; index < Len/4; index++)
    {
        if (HAL_RNG_GenerateRandomNumber(&hrng, &randomValue) == HAL_OK)
        {
            *oLen += 4;
            memset(&(Output[index * 4]), (int)randomValue, 4);
        }
        else
        {
            Error_Handler();
        }
    }
  
    return 0;
}

#endif /*MBEDTLS_ENTROPY_HARDWARE_ALT*/
```

## 使用mbedtls CTR_DRBG接口生成随机数

### 宏配置

使用mbedtls随机数生成功能需要开启以下宏：

| 宏定义                             | 功能                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES | 不使用默认熵源**（如果已有、要屏蔽该宏）**<br>定义此宏，以防止加在默认的熵函数<br>基于mbedtls_timing_hardclock和基于HAVEGE的轮询功能。 |
| MBEDTLS_NO_PLATFORM_ENTROPY        | 不使用系统内置熵源                                           |
| MBEDTLS_AES_C                      | 使用AES算法                                                  |
| MBEDTLS_ENTROPY_C                  | 使能熵源模块                                                 |
| MBEDTLS_CTR_DRBG_C                 | 使能随机数模块                                               |
| MBEDTLS_ENTROPY_FORCE_SHA256       | 使能SHA256算法                                               |
| MBEDTLS_AES_ROM_TABLES             | 将AES表存储在ROM中（节约内存空间）                           |

根据以上代码，编写针对本实验的配置文件`mbedtls_config_ctr_drbg.h`：

cmakefile.txt中添加`CPPFLAGS += -DMBEDTLS_CONFIG_FILE='"mbedtls/mbedtls_config_ctr_drbg.h"'`

```c
#ifndef _MBEDTLS_CONFIG_CTR_DRBG_H_
#define _MBEDTLS_CONFIG_CTR_DRBG_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT						// 使用自己的硬件熵
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES		// 不定义此宏，系统将调用默认的熵函数
#define MBEDTLS_NO_PLATFORM_ENTROPY							// 不使用系统内置熵源

/* mbed modules */
#define MBEDTLS_AES_C														// 使用AES算法
#define MBEDTLS_AES_ROM_TABLES									// 将aes表存储在rom中
#define MBEDTLS_CTR_DRBG_C											// 使能随机数模块
#define MBEDTLS_ENTROPY_C												// 使能熵源模块
#define MBEDTLS_SHA256_C												// 使能sha算法

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_CTR_DRBG_H_ */
```

### API说明

#### 熵相关

① 初始化熵

```c
void mbedtls_entropy_init( mbedtls_entropy_context *ctx );
```

② 释放熵

```c
void mbedtls_entropy_free( mbedtls_entropy_context *ctx );
```

③ 错误码说明（用于根据返回值判定错误）

```c
#define MBEDTLS_ERR_ENTROPY_SOURCE_FAILED                 -0x003C  /**< Critical entropy source failure. */
#define MBEDTLS_ERR_ENTROPY_MAX_SOURCES                   -0x003E  /**< No more sources can be added. */
#define MBEDTLS_ERR_ENTROPY_NO_SOURCES_DEFINED            -0x0040  /**< No sources have been added to poll. */
#define MBEDTLS_ERR_ENTROPY_NO_STRONG_SOURCE              -0x003D  /**< No strong sources have been added to poll. */
#define MBEDTLS_ERR_ENTROPY_FILE_IO_ERROR                 -0x003F  /**< Read/write error in file. */
```

#### crt_drbg相关

① 初始化ctr_drbg随机数种子

```c
void mbedtls_ctr_drbg_init( mbedtls_ctr_drbg_context *ctx );
```

② 根据熵生成随机数种子

```c
/**
 * \brief               This function seeds and sets up the CTR_DRBG
 *                      entropy source for future reseeds.
 *
 * \note Personalization data can be provided in addition to the more generic
 *       entropy source, to make this instantiation as unique as possible.
 *
 * \param ctx           The CTR_DRBG context to seed.
 * \param f_entropy     The entropy callback, taking as arguments the
 *                      \p p_entropy context, the buffer to fill, and the
                        length of the buffer.
 * \param p_entropy     The entropy context.
 * \param custom        Personalization data, that is device-specific
                        identifiers. Can be NULL.
 * \param len           The length of the personalization data.
 *
 * \return              \c 0 on success, or
 *                      #MBEDTLS_ERR_CTR_DRBG_ENTROPY_SOURCE_FAILED on failure.
 */
int mbedtls_ctr_drbg_seed( mbedtls_ctr_drbg_context *ctx,
                   int (*f_entropy)(void *, unsigned char *, size_t),
                   void *p_entropy,
                   const unsigned char *custom,
                   size_t len );
```

③ 根据随机数种子生成随机数

```c
int mbedtls_ctr_drbg_random( void *p_rng, unsigned char *output, size_t output_len );
```

> 最大生成长度是：MBEDTLS_CTR_DRBG_MAX_REQUEST（1024），如果需要修改的话可以在配置文件中定义该宏。

④ 释放ctr_drbg随机数

```
void mbedtls_ctr_drbg_free( mbedtls_ctr_drbg_context *ctx );
```

⑤ 错误码

```c
#define MBEDTLS_ERR_CTR_DRBG_ENTROPY_SOURCE_FAILED        -0x0034  /**< The entropy source failed. */
#define MBEDTLS_ERR_CTR_DRBG_REQUEST_TOO_BIG              -0x0036  /**< The requested random buffer length is too big. */
#define MBEDTLS_ERR_CTR_DRBG_INPUT_TOO_BIG                -0x0038  /**< The input (entropy + additional data) is too large. */
#define MBEDTLS_ERR_CTR_DRBG_FILE_IO_ERROR                -0x003A  /**< Read or write error in file. */
```

#### 编写测试代码

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_CTR_DRBG_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/entropy.h"
#include "mbedtls/ctr_drbg.h"

int mbedtls_ctr_drbg_test(void)
{
    int ret;
    uint8_t data_buf[10];
   
    const char *pers = "crbg_test";
    mbedtls_entropy_context entropy;			// 熵
    mbedtls_ctr_drbg_context ctr_drbg;		// 随机数种子

    mbedtls_entropy_init(&entropy);				// 初始化熵
    mbedtls_ctr_drbg_init(&ctr_drbg);			// 初始化随机数种子
    
  	// 根据熵生成随机数种子
    if( ( ret = mbedtls_ctr_drbg_seed( &ctr_drbg, mbedtls_entropy_func, &entropy,
                               (const unsigned char *) pers,
                               strlen( pers ) ) ) != 0 ) {
        printf( "\n  . Seeding the random number generator... failed\n  ! mbedtls_ctr_drbg_seed returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }

    printf( "\n  . Seeding the random number generator... ok\n" );

		// 根据随机数种子生成随机数
    if ( ( ret = mbedtls_ctr_drbg_random(&ctr_drbg, data_buf, sizeof(data_buf) ) ) != 0) {
        printf( "\n  . generate random data... failed\n  ! mbedtls_ctr_drbg_random returned %d\n", ret );
        goto exit;
    }
    printf( "\n  . generate random data... ok\n" );
    
  	// 打印随机数
    printf("random data:[");
    for (int i = 0; i < sizeof(data_buf); i++) {
        printf("%02x ", data_buf[i]);
    }
    printf("]\r\n");
    
    exit:
    
    /* 5. 释放随机数种子 */
    mbedtls_ctr_drbg_free(&ctr_drbg);
    
    /* 6. 释放熵*/
    mbedtls_entropy_free(&entropy);
    
    return ret;
}

#endif /* MBEDTLS_CTR_DRBG_C */
```



# 二、单向散列算法的配置与使用(MD5-SHA1-SHA256-SHA512)

## 单向散列算法

### 单向散列函数

单向散列函数是一类满足密码学算法安全属性的特殊散列函数，可以根据消息的内容计算出散列值，又称为安全散列函数或者哈希函数，通常用于**检验消息完整性**。
输入数据称为**消息**，计算出的散列值称为**消息摘要（摘要）**。
单向散列函数具有如下特点：

- 输入长度任意；
- **输出长度固定**；
- **单向性**：无法根据散列值还原出消息；
单向散列函数主要用在：
- 消息完整性检测；
- 构造伪随机数生成算法；
- 消息认证码；
- 数字签名；
- 一次性口令；

### 单向散列算法

单向散列算法是单向散列函数的实现，主要包括两大算法实现：MD4/5系列实现、SHA系列实现。

####  MD系列实现

MD系列算法最早是MD4，后来诞生了MD5，两者都可以产生128 bit 的散列值。

#### SHA系列算法

SHA系列算法由美国国家标准与技术研究所NIST确定，主要包含以下几个：

- SHA0：1993年发布，可以产生160 bit 的消息摘要，但是存在重大缺陷被撤销；
- SHA1：1995年发布，可以产生160 bit 的消息摘要，也存在缺陷（不推荐使用）；
- SHA2：包括SHA256算法、SHA384算法、SHA512算法；
- SHA3：支持与SHA2相同的消息摘要长度，但是算法内部结构不同；

### mbedtls中提供的单向散列算法
- MD2
- MD4
- MD5
- SHA1
- SHA224
- SHA256
- SHA384
- SHA512

## 功能模块的使用

### 配置宏

mbedtls中提供的这些单向散列算法，**每个都是一个独立的模块，由对应的宏控制是否开启**：

```c
#define MBEDTLS_MD2_C
#define MBEDTLS_MD4_C
#define MBEDTLS_MD5_C
#define MBEDTLS_SHA1_C
#define MBEDTLS_SHA224_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_SHA384_C
#define MBEDTLS_SHA512_C
```

在使用的时候，除了可以使用每个功能模块提供的API，还可以使用**md通用接口**，通过下面的宏开启：

```c
#define MBEDTLS_MD_C
```

测试SHA1、SHA256、SHA512三个算法，所以编写一个新的配置头文件`mbedtls_config_sha_x.h`，内容如下：

cmakefile.txt中添加`CPPFLAGS += -DMBEDTLS_CONFIG_FILE='"mbedtls_config_sha_x.h"'`

```c
/**
 * @brief   Minimal configuration for SHAX Function
 * @author  mculover666
 * @date    2020/09/22
*/

#ifndef _MBEDTLS_CONFIG_SHA_X_H_
#define _MBEDTLS_CONFIG_SHA_X_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
//#define MBEDTLS_MD2_C
//#define MBEDTLS_MD4_C
//#define MBEDTLS_MD5_C
#define MBEDTLS_SHA1_C
//#define MBEDTLS_SHA224_C
#define MBEDTLS_SHA256_C
//#define MBEDTLS_SHA384_C
#define MBEDTLS_SHA512_C

/* enable generic message digest wrappers */
#define MBEDTLS_MD_C

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_SHA_X_H_ */

```

### md通用接口api说明

头文件：

```c
#include "mbedtls/md.h"
```

① 初始化MD结构体：

```c
void mbedtls_md_init( mbedtls_md_context_t *ctx );
```

② 根据算法类型得到md信息结构体指针：

```c
const mbedtls_md_info_t *mbedtls_md_info_from_type( mbedtls_md_type_t md_type );
```

其中算法类型 mbedtls_md_type_t 是枚举类型，有以下值：

```c
typedef enum {
    MBEDTLS_MD_NONE=0,    /**< None. */
    MBEDTLS_MD_MD2,       /**< The MD2 message digest. */
    MBEDTLS_MD_MD4,       /**< The MD4 message digest. */
    MBEDTLS_MD_MD5,       /**< The MD5 message digest. */
    MBEDTLS_MD_SHA1,      /**< The SHA-1 message digest. */
    MBEDTLS_MD_SHA224,    /**< The SHA-224 message digest. */
    MBEDTLS_MD_SHA256,    /**< The SHA-256 message digest. */
    MBEDTLS_MD_SHA384,    /**< The SHA-384 message digest. */
    MBEDTLS_MD_SHA512,    /**< The SHA-512 message digest. */
    MBEDTLS_MD_RIPEMD160, /**< The RIPEMD-160 message digest. */
} mbedtls_md_type_t;
```

③ 设置md结构体，完成md结构体内部接口初始化：

```c
int mbedtls_md_setup( mbedtls_md_context_t *ctx, const mbedtls_md_info_t *md_info, int hmac );
```

④ 获取单向散列算法名称：

```c
const char *mbedtls_md_get_name( const mbedtls_md_info_t *md_info );
```

⑤ 获取单向散列算法输出消息摘要的长度：

```c
unsigned char mbedtls_md_get_size( const mbedtls_md_info_t *md_info );
```

⑥ md启动接口：

```c
int mbedtls_md_starts( mbedtls_md_context_t *ctx );
```

⑦ md更新接口，处理输入数据，计算散列值：

```c
int mbedtls_md_update( mbedtls_md_context_t *ctx, const unsigned char *input, size_t ilen );
```

⑧ md完成接口：

```c
int mbedtls_md_finish( mbedtls_md_context_t *ctx, unsigned char *output );
```

⑨ 释放md结构体：

```c
void mbedtls_md_free( mbedtls_md_context_t *ctx );
```

⑩ 错误码：

```c
#define MBEDTLS_ERR_MD_FEATURE_UNAVAILABLE                -0x5080  /**< The selected feature is not available. */
#define MBEDTLS_ERR_MD_BAD_INPUT_DATA                     -0x5100  /**< Bad input parameters to function. */
#define MBEDTLS_ERR_MD_ALLOC_FAILED                       -0x5180  /**< Failed to allocate memory. */
#define MBEDTLS_ERR_MD_FILE_IO_ERROR                      -0x5200  /**< Opening or reading of file failed. */
```

**调用过程为：**

- starts：初始化，只需调用一次；
- update：计算值，可以多次调用；
- finish：获取计算出的消息摘要；

新建文件`mbedtls_sha_x_test.c`，编写如下测试函数：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_MD_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/md.h"

int mbedtls_shax_test(mbedtls_md_type_t md_type)
{
    int len, i;
    int ret;
    const char *message = "mculover666";
    unsigned char digest[32];
    
    mbedtls_md_context_t ctx;
    const mbedtls_md_info_t *info;

    printf("message is:%s\r\n", message);

    /* 1. init mbedtls_md_context_t structure */
    mbedtls_md_init(&ctx);
    
    /* 2. get md info structure pointer */
    info = mbedtls_md_info_from_type(md_type);
    
    /* 3. setup md info structure */
    ret = mbedtls_md_setup(&ctx, info, 0);
    if (ret != 0) {
        goto exit;
    }
    
    /* 4. start */
    ret = mbedtls_md_starts(&ctx);
     if (ret != 0) {
        goto exit;
    }
     
    /* 5. update */
    ret = mbedtls_md_update(&ctx, (unsigned char *)message, strlen(message));
    if (ret != 0) {
        goto exit;
    }
     
    /* 6. finish */
    ret = mbedtls_md_finish(&ctx, digest);
    if (ret != 0) {
        goto exit;
    }
    
    /* show */
    printf("%s digest context is:[", mbedtls_md_get_name(info));
    len= mbedtls_md_get_size(info);
    for (i = 0; i < len; i++) {
      printf("%02x", digest[i]);
    }
    printf("]\r\n");

    exit:
    /* 7. free */
    mbedtls_md_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_MD_C */
```

# 三、对称加密算法的配置与使用（AES算法）

## AES对称加密算法

### 什么是对称加密算法

对称算法是一种通信双方使用**相同的秘钥**进行加密和解密的密码算法。

其中这份相同的秘钥称为对称秘钥或者共享秘钥，只有通信双方持有，如果传输的密文被拦截，没有秘钥也无法解密出原文。

### 对称加密算法的几种模式

#### 分组密码模式

分组密码只能加密或者加密**固定长度**的数据，如果需要加密的明文长度超过了分组长度，则需要对明文进行分组，然后对各分组进行处理。

① **ECB模式（电子密码本模式）**

ECB（Electronic CodeBook）模式将明文进行分组，然后针对每组单独进行加密，处理之后再将每组密文合并在一起，同理，解析也是一样的过程。

这种模式之下明文和密文一一对应，存在明显的缺点，所以实际中禁止使用。

② **CBC模式（密码分组链接模式）**

CBC（Cipher Block Chaining）模式中，每组明文在加密前都要先与前面的一组密文进行异或操作，然后使用秘钥计算出密文。

同理，每组密文在解密时先使用秘钥计算出明文，然后与前面的一组密文进行异或运算，还原出最终明文。

在这种方式下，由于第一个明文分组前面没有密文分组，所以需要提前设置一个与分组长度相等的序列来代替密文分组，称之为初始化向量（Intialization Vector）。一般在发送端使用伪随机数生成器生成一个初始化的IV，然后通过某种方式发送给接收端。

所以，**在CBC模式下，要想通信双方能正常加密解密，除了拥有相同的秘钥，还必须要拥有相同的初始化向量**。

③ **CRT模式（计数器模式）**

CRT模式中，使用与分组长度的计数值参与运算。通过对逐次累加的计数器值进行加密来生成密钥流，然后秘钥流与明文分组进行异或运算，得到密文分组。

同理，接收端解密的时候：通过对逐次累加的计数器值进行加密来生成密钥流，然后秘钥流与密文分组进行异或运算，得到明文分组。

④ 三种模式对比

相比之下：

- CTR模式中对于每个分组的加密/解密是独立的，可以进行并行计算，大幅提高计算效率；
- CBC模式中密文分组的解密过程依赖于前一个密文分组，而CRT模式中每个密文分组的解密过程是独立的；
- CTR模式中仅需要加密算法（对计数值加密）、不需要解密算法，而ECB和CBC模式都需要加密和解密算法；
- CTR模式不需要对明文进行填充，而ECB和CBC模式需要；

#### PKCS7填充方案

在EBC模式和CBC模式中，要求**输入明文长度不是分组长度的整数倍时，要对明文进行填充**。

常用的填充方案是 PKCS7 方案，规则如下：

### AES算法

AES算法的固定分组大小为128位（16字节），秘钥长度为128、192、256位。

AES算法中的S盒是唯一的非线性实现，解密过程中需要使用S盒完整字节替换，**通常S盒计算通过查表法实现**。

### mbedtls中提供的对称加密算法

mbedtls中提供的对称加密算法如下：

- AES：支持ECB、CBC、CTR、CFB、GCM模式；
- ARCFOUR(RC4)
- Blowfish
- Camellia
- DES/3DES
- XTEA

## AES功能模块的配置与使用

### 配置宏定义

#### AES功能相关宏

mbedtls中提供的这些对称加密算法，每个都是一个独立的模块，由对应的宏控制是否开启，要使用AES相关功能，需要开启以下宏：

| 宏定义                       | 说明              |
| ---------------------------- | ----------------- |
| MBEDTLS_AES_C                | 开启AES算法       |
| MBEDTLS_CIPHER_MODE_CBC      | 开启CBC模式       |
| MBEDTLS_CIPHER_MODE_CTR      | 开启CRT模式       |
| MBEDTLS_AES_ROM_TABLES       | 开启预定义S盒     |
| MBEDTLS_CIPHER_PADDING_PKCS7 | 开启PKCS7填充方案 |

① **MBEDTLS_AES_C**

```c
#define MBEDTLS_AES_C
```

② **MBEDTLS_CIPHER_MODE_CBC**

```c
#define MBEDTLS_CIPHER_MODE_CBC
```

③ **MBEDTLS_CIPHER_MODE_CTR**

```c
#define MBEDTLS_CIPHER_MODE_CTR
```

④ **MBEDTLS_AES_ROM_TABLES**

```c
#define MBEDTLS_AES_ROM_TABLES
```

⑤**MBEDTLS_CIPHER_PADDING_PKCS7**

```c
#define MBEDTLS_CIPHER_PADDING_PKCS7
```

#### cipher通用接口

除了使用功能模块所提供的接口，mbedtls还提供了cipher通用接口，通过宏定义 **MBEDTLS_CIPHER_C** 开启：

```c
#define MBEDTLS_CIPHER_C
```

#### 编写本实验配置文件

编写`mbedtls_config_aes.h`文件，作为针对本实验的mbedtls配置文件，内容如下：

```c
#ifndef _MBEDTLS_CONFIG_AES_H_
#define _MBEDTLS_CONFIG_AES_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_CIPHER_MODE_CBC				// 开启CBC模式
#define MBEDTLS_CIPHER_MODE_CTR				// 开启CRT模式
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_AES_C									// 开启AES算法
#define MBEDTLS_AES_ROM_TABLES				// 开启预定义S盒
#define MBEDTLS_CIPHER_PADDING_PKCS7	// 开启PKCS7填充方案
#define MBEDTLS_CIPHER_C

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_CTR_DRBG_H_ */
```

### cipher通用接口API说明

包含头文件：

```c
#include "mbedtls/cipher.h"
```

① 初始化cipher结构体：

```c
void mbedtls_cipher_init( mbedtls_cipher_context_t *ctx );
```

② 通过加密算法名类型获取cipher信息结构体指针：

```c
const mbedtls_cipher_info_t *mbedtls_cipher_info_from_type( const mbedtls_cipher_type_t cipher_type );
```

③ 设置cipher结构体：

```c
int mbedtls_cipher_setup( mbedtls_cipher_context_t *ctx,
                          const mbedtls_cipher_info_t *cipher_info );
```

④ 获取算法名称：

```c
static inline const char *mbedtls_cipher_get_name(
    const mbedtls_cipher_context_t *ctx )
{
    MBEDTLS_INTERNAL_VALIDATE_RET( ctx != NULL, 0 );
    if( ctx->cipher_info == NULL )
        return 0;

    return ctx->cipher_info->name;
}
```

⑤ 获取算法分组长度：

```c
static inline unsigned int mbedtls_cipher_get_block_size(
    const mbedtls_cipher_context_t *ctx )
{
    MBEDTLS_INTERNAL_VALIDATE_RET( ctx != NULL, 0 );
    if( ctx->cipher_info == NULL )
        return 0;

    return ctx->cipher_info->block_size;
}
```

⑥ 设置解密秘钥接口：

```c
int mbedtls_cipher_setkey( mbedtls_cipher_context_t *ctx,
                           const unsigned char *key,
                           int key_bitlen,
                           const mbedtls_operation_t operation );
```

⑦ 设置iv接口：

```c
int mbedtls_cipher_set_iv( mbedtls_cipher_context_t *ctx,
                           const unsigned char *iv,
                           size_t iv_len );
```

⑧ cipher更新接口：

```c
int mbedtls_cipher_update( mbedtls_cipher_context_t *ctx,
                           const unsigned char *input,
                           size_t ilen, unsigned char *output,
                           size_t *olen );
```

⑨ cipher完成接口：

```c
int mbedtls_cipher_finish( mbedtls_cipher_context_t *ctx,
                   unsigned char *output, size_t *olen );
```

⑩ 释放cipher结构体：

```c
void mbedtls_cipher_free( mbedtls_cipher_context_t *ctx );
```

### 编写测试函数

新建demo文件`mbedtls_aes_test.c`，编写以下测试内容：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_CIPHER_C)

#include <stdio.h>
#include <string.h>
#include <stdint.h>
#include "mbedtls/cipher.h"

/* Private Key */
uint8_t key[16] = {
    0x06, 0xa9, 0x21, 0x40, 0x36, 0xb8, 0xa1, 0x5b,
    0x51, 0x2e, 0x03, 0xd5, 0x34, 0x12, 0x00, 0x06,
};

/* Intialization Vector */
uint8_t iv[16] = {
    0x3d, 0xaf, 0xba, 0x42, 0x9d, 0x9e, 0xb4, 0x30,
    0xb4, 0x22, 0xda, 0x80, 0x2c, 0x9f, 0xac, 0x41,
};

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    printf("buf:");
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

int aes_test(mbedtls_cipher_type_t cipher_type)
{
    int ret;
    size_t len;
    int olen = 0;
    uint8_t output_buf[64];
    const char *input = "creekwater is learning aes of mbedtls.";
    
    mbedtls_cipher_context_t ctx;
    const mbedtls_cipher_info_t *info;
    
    /* 1. 初始化cipher context结构体 */
    mbedtls_cipher_init(&ctx);
    
    /* 2. 从类型获取cipher info */
    info = mbedtls_cipher_info_from_type(cipher_type);
    
    /* 3. 设置cipher context结构体 */
    ret = mbedtls_cipher_setup(&ctx, info);
    if (ret != 0) {
        goto exit;
    }
    
    /* 4. 设置key */
    ret = mbedtls_cipher_setkey(&ctx, key, sizeof(key) * 8, MBEDTLS_ENCRYPT);
    if (ret != 0) {
        goto exit;
    }
    
    /* 5. 设置iv */
    ret = mbedtls_cipher_set_iv(&ctx, iv, sizeof(iv));
    if (ret != 0) {
        goto exit;
    }
    
    /* 6. 更新 cipher */
    ret = mbedtls_cipher_update(&ctx, (unsigned char *)input, strlen(input), output_buf, &len);
    if (ret != 0) {
        goto exit;
    }
    olen += len;
    
    /* 7. 结束 cipher */
    ret = mbedtls_cipher_finish(&ctx, output_buf, &len);
    if (ret != 0) {
        goto exit;
    }
    olen += len;
    
    /* 打印结果 */
    printf("\r\nsource_context:%s\r\n", input);
    printf("cipher name:%s block size is:%d\r\n", mbedtls_cipher_get_name(&ctx), mbedtls_cipher_get_block_size(&ctx));
    dump_buf(output_buf, olen);
    
    exit:
    /* 8. 释放 cipher 结构体 */
    mbedtls_cipher_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_CIPHER_C */
```

### 测试结果

从结果可以看到：

- AES算法分组大小为16个字节（128位）；
- CBC模式下，AES算法会将55个字节的明文加密为64个字节；
- CRT模式下，AES算法加密之后的密文长度仍然是55个字节，和明文长度相同；

# 四、消息认证码

## 什么是消息认证码

消息认证码（Message Authentication Code）可**用来检查消息的完整性和消息来源的可靠性**。

消息认证码算法的输入为任意长度的消息和通信双方共享的秘钥，消息认证码的输出为固定长度的数据，该输出数据称为MAC值、Tag值、T值。

## 消息认证码的实现方式

### 基于单向散列算法实现

这类实现称为**HMAC**，比如HMAC-SHA1、HMAC-SHA256等。因为单向散列算法无法验证消息来源的可靠性，所以不能直接用于生成消息认证码。

另外，在HMAC算法中，**通信双方共享秘钥没有长度限制，明文也没有长度限制，但是最后生成的消息认证码长度是固定的**。

比如基于SHA256算法的HAMC算法，因为SHA256计算出的消息摘要是256字节，32个字节，所以最后生成的消息认证码长度也是32个字节固定长度。

### 基于分组加密算法实现

比如将CBC分组加密结果的最后一个分组作为消息认证码的**CBC-MAC**算法。

还有通过共享秘钥派生出两个中间秘钥的**CMAC**算法，安全性更高。

### 认证加密算法实现

认证加密算法在通信过程中提供数据机密性和完整性的密码算法，是对称加密算法（eg. AES）和消息认证码的结合，典型实现包括**GCM**、**CCM**等。

CCM认证加密过程对明文进行两次处理，第一次使用CBC-MAC计算消息认证码，第二次使用CRT模式将消息认证码（明文）加密。

GCM认证加密过程和CCM类似，只不过第一次计算使用的是GHASH算法，第二次计算使用的是GCTR算法。

另外，**GCM的消息认证码长度只能为16字节，而CCM模式的消息认证码长度最小为4字节、最大为16字节**。

## mbedtls中的消息认证码实现

mbedtls中提供了HMAC计算接口和GCM计算接口。

## 配置宏

开启一种单向散列函数功能和MD通用接口即可：

| 宏定义           | 说明                   |
| ---------------- | ---------------------- |
| MBEDTLS_MD_C     | 开启MD通用接口         |
| MBEDTLS_SHA256_C | 开启SHA256单向散列算法 |

新建一个针对本实验的配置文件`mbedtls_config_hmac.h`：

```c
#ifndef _MBEDTLS_CONFIG_SHA_X_H_
#define _MBEDTLS_CONFIG_SHA_X_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
//#define MBEDTLS_MD2_C
//#define MBEDTLS_MD4_C
//#define MBEDTLS_MD5_C
//#define MBEDTLS_SHA1_C
//#define MBEDTLS_SHA224_C
#define MBEDTLS_SHA256_C
//#define MBEDTLS_SHA384_C
//#define MBEDTLS_SHA512_C

/* enable generic message digest wrappers */
#define MBEDTLS_MD_C

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_SHA_X_H_ */
```

cmakefile.txt中添加`CPPFLAGS += -DMBEDTLS_CONFIG_FILE='"mbedtls_config_hmac.h"'`

## HMAC功能模块API说明

① hmac启动接口（完成秘钥、ipad、opad计算）：

```c
int mbedtls_md_hmac_starts( mbedtls_md_context_t *ctx, const unsigned char *key,
                    size_t keylen );
```

② hmac更新接口（处理输入数据）：

```c
int mbedtls_md_hmac_update( mbedtls_md_context_t *ctx, const unsigned char *input,
                    size_t ilen );
```

③ hmac完成接口（输出消息认证码）：

```c
int mbedtls_md_hmac_finish( mbedtls_md_context_t *ctx, unsigned char *output);
```

④ 错误码：

```c
#define MBEDTLS_ERR_MD_FEATURE_UNAVAILABLE                -0x5080  /**< The selected feature is not available. */
#define MBEDTLS_ERR_MD_BAD_INPUT_DATA                     -0x5100  /**< Bad input parameters to function. */
#define MBEDTLS_ERR_MD_ALLOC_FAILED                       -0x5180  /**< Failed to allocate memory. */
#define MBEDTLS_ERR_MD_FILE_IO_ERROR                      -0x5200  /**< Opening or reading of file failed. */

/* MBEDTLS_ERR_MD_HW_ACCEL_FAILED is deprecated and should not be used. */
#define MBEDTLS_ERR_MD_HW_ACCEL_FAILED                    -0x5280  /**< MD hardware accelerator failed. */
```

特别注意，与计算单向散列值不同，**在计算消息认证码的时候要在设置时表明使用HMAC**，也就是 mbedtls_md_setup 函数的第三个参数要设置为非零值，通常设为1即可：

```c
int mbedtls_md_setup( mbedtls_md_context_t *ctx, const mbedtls_md_info_t *md_info, int hmac );
```

## 编写测试函数

新建文件`mbedtls_hmac_test.c`，编写如下测试函数：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_MD_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/md.h"

int mbedtls_hmac_test(mbedtls_md_type_t md_type)
{
    int len, i;
    int ret;
    const char *key     = "mculover666";
    const char *message = "hello world";
    unsigned char hmac[32];
    
    mbedtls_md_context_t ctx;
    const mbedtls_md_info_t *info;

    printf("message is:%s\r\n", message);

    /* 1. 初始化 mbedtls_md_context_t 结构体 */
    mbedtls_md_init(&ctx);
    
    /* 2. get md info structure pointer */
    info = mbedtls_md_info_from_type(md_type);
    
    /* 3. setup md info structure */
    ret = mbedtls_md_setup(&ctx, info, 1);
    if (ret != 0) {
        goto exit;
    }
    
    /* 4. start */
    ret = mbedtls_md_hmac_starts(&ctx, (unsigned char *)key, strlen(key));
    if (ret != 0) {
        goto exit;
    }
     
    /* 5. update */
    ret = mbedtls_md_hmac_update(&ctx, (unsigned char *)message, strlen(message));
    if (ret != 0) {
        goto exit;
    }
     
    /* 6. finish */
    ret = mbedtls_md_hmac_finish(&ctx, hmac);
    if (ret != 0) {
        goto exit;
    }
    
    /* show */
    printf("%s hmac context is:[", mbedtls_md_get_name(info));
    len= mbedtls_md_get_size(info);
    for (i = 0; i < len; i++) {
      printf("%02x", hmac[i]);
    }
    printf("]\r\n");

    exit:
    /* 7. free */
    mbedtls_md_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_MD_C */
```

## 测试结果

在main.c中声明该测试函数：

```c
/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */

#include "mbedtls/md.h"
int mbedtls_hmac_test(mbedtls_md_type_t md_type);

/* USER CODE END 0 */
```

然后在main函数中调用：

```c
/* 3. hamc test */
mbedtls_hmac_test(MBEDTLS_MD_SHA256);
```

编译、下载、测试结果如图：

在MDK中配置：
![img](https://img-blog.csdnimg.cn/20200926151128537.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

## GCM功能模块相关的API说明

① cipher认证加密：

```c
int mbedtls_cipher_auth_encrypt( mbedtls_cipher_context_t *ctx,
                         const unsigned char *iv, size_t iv_len,
                         const unsigned char *ad, size_t ad_len,
                         const unsigned char *input, size_t ilen,
                         unsigned char *output, size_t *olen,
                         unsigned char *tag, size_t tag_len );
```

② cipher认证解密

```c
int mbedtls_cipher_auth_decrypt( mbedtls_cipher_context_t *ctx,
                         const unsigned char *iv, size_t iv_len,
                         const unsigned char *ad, size_t ad_len,
                         const unsigned char *input, size_t ilen,
                         unsigned char *output, size_t *olen,
                         const unsigned char *tag, size_t tag_len );
```

## 编写测试代码

新建文件`mbedtls_config_gcm.c`，编写测试代码：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_GCM_C)

#include <stdio.h>
#include <string.h>
#include <stdint.h>
#include "mbedtls/cipher.h"

/* source context */
static const char input[16] = {
    0xc3, 0xb3, 0xc4, 0x1f, 0x11, 0x3a, 0x31, 0xb7, 
    0x3d, 0x9a, 0x5c, 0xd4, 0x32, 0x10, 0x30, 0x69
};
/* Private Key */
static uint8_t key[16] = {
    0xc9, 0x39, 0xcc, 0x13, 0x39, 0x7c, 0x1d, 0x37,
    0xde, 0x6a, 0xe0, 0xe1, 0xcb, 0x7c, 0x42, 0x3c
};

/* Intialization Vector */
static uint8_t iv[12] = {
    0xb3, 0xd8, 0xcc, 0x01, 
    0x7c, 0xbb, 0x89, 0xb3,
    0x9e, 0x0f, 0x67, 0xe2
};

/* The additional data to authenticate */
uint8_t add[16] = {
    0x24, 0x82, 0x56, 0x02, 0xbd, 0x12, 0xa9, 0x84, 
    0xe0, 0x09, 0x2d, 0x3e, 0x44, 0x8e, 0xda, 0x5f
};

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

int gcm_test(mbedtls_cipher_type_t cipher_type)
{
    int ret;
    size_t len;
    int olen = 0;
    uint8_t output_buf[16];
    uint8_t tag_buf[16];
    uint8_t decrypt_out_buf[16];
    
    
    mbedtls_cipher_context_t ctx;
    const mbedtls_cipher_info_t *info;
    
    /* 1. init cipher structuer */
    mbedtls_cipher_init(&ctx);
    
    /* 2. get info structuer from type */
    info = mbedtls_cipher_info_from_type(cipher_type);
    
    /* 3. setup cipher structuer */
    ret = mbedtls_cipher_setup(&ctx, info);
    if (ret != 0) {
        goto exit;
    }
    
    /* 4. set encrypt key */
    ret = mbedtls_cipher_setkey(&ctx, key, sizeof(key) * 8, MBEDTLS_ENCRYPT);
    if (ret != 0) {
        goto exit;
    }
    
    /* 5. auth encrypt */
    ret = mbedtls_cipher_auth_encrypt(&ctx, 
                                      iv, sizeof(iv), add, sizeof(add), 
                                      (unsigned char *)input, sizeof(input), 
                                      output_buf, &len, tag_buf, sizeof(tag_buf));
    if (ret != 0) {
        goto exit;
    }
    olen += len;
    
    /* show */
    printf("cipher name:%s block size is:%d\r\n", mbedtls_cipher_get_name(&ctx), mbedtls_cipher_get_block_size(&ctx));
    printf("\r\noutput_buf:\r\n");
    dump_buf((uint8_t *)output_buf, olen);
    printf("\r\ntag_buf:\r\n");
    dump_buf(tag_buf, sizeof(tag_buf));
    
    /* 6. set decrypt key */
    ret = mbedtls_cipher_setkey(&ctx, key, sizeof(key) * 8, MBEDTLS_DECRYPT);
    if (ret != 0) {
        goto exit;
    }
    
    /* 7. auth decrypt */
    olen = 0;
    len  = 0;
    ret = mbedtls_cipher_auth_decrypt(&ctx, 
                                      iv, sizeof(iv), add, sizeof(add), 
                                      (unsigned char *)output_buf, sizeof(output_buf), 
                                      decrypt_out_buf, &len, tag_buf, sizeof(tag_buf));
    if (ret != 0) {
        goto exit;
    }
    olen += len;
    
    /* show */
    printf("cipher name:%s block size is:%d\r\n", mbedtls_cipher_get_name(&ctx), mbedtls_cipher_get_block_size(&ctx));
    printf("\r\ndecrypt_out_buf:\r\n");
    dump_buf((uint8_t *)decrypt_out_buf, olen);
    printf("\r\ntag_buf:\r\n");
    dump_buf(tag_buf, sizeof(tag_buf));
    
    exit:
    /* 8. free cipher structure */
    mbedtls_cipher_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_CIPHER_C */
```

## 测试结果

在main.c 中声明该外部函数：

```c
/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */
#include "mbedtls/cipher.h"
int gcm_test(mbedtls_cipher_type_t cipher_type);

/* USER CODE END 0 */
```

接着在main函数中调用测试函数：

```c
/* 4.gcm test */
gcm_test(MBEDTLS_CIPHER_AES_128_GCM);
```

编译、下载，测试结果为：
![img](https://img-blog.csdnimg.cn/20200926151709761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 五、非对称加密算法的配置与使用（RSA算法）

## 非对称加密算法——RSA

### 对称加密算法和非对称加密算法

对称加密算法，比如AES算法，在发送端（加密）和接收端（解密）使用相同的一份秘钥，称为共享秘钥。

非对称加密算法，比如RSA算法，在加密使用**公钥**，而在解密时使用**私钥**。

### RSA算法

RSA算法是一种常用的非对称加密算法。

RSA算法主要包括三个过程：

- 生成秘钥对
- 使用公钥加密明文
- 使用私钥解密密文

### RSA加速技术

RSA算法在计算过程中存在较多的取模运算和幂运算，计算速度比对称加密算法要慢，所以不适合对大量数据进行加密和解密，在实际中常用于加密或解密小数据片段。

RSA公钥加密过程可使用短公开指数进行快速计算，而RSA私钥解密过程可使用中国剩余定理（CRT）进行加速执行。

### RSA填充方法

RSA加密结果是**确定的**。当公钥确定的时候，一段明文总是会加密的到对应的密文，这样未免存在安全隐患。

实际使用RSA算法中需要包含填充方案，在计算之前会对明文进行随机注入，这样在公钥和明文相同的情况下，也不会生成相同的秘文。

常用的RSA填充方案有两种：RSAES-OAEP（推荐使用）和RSAES-PKCS1V1_5（较早、不推荐使用）。

## RSA功能模块的配置与使用

### 配置宏

使用RSA功能需要提前开启伪随机数生成器（依赖AES算法、SHA256算法、MD通用接口），其中伪随机数生成器的硬件适配移植实现已经讲述，请参考对应的第二篇博客，不再赘述。

综合上述，本实验中需要开启的宏如下表：

| 宏定义                             | 功能                                       |
| ---------------------------------- | ------------------------------------------ |
| MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES | 不使用默认熵源**（如果已有、要屏蔽该宏）** |
| MBEDTLS_NO_PLATFORM_ENTROPY        | 不使用系统内置熵源                         |
| MBEDTLS_AES_C                      | 使用AES算法                                |
| MBEDTLS_ENTROPY_C                  | 使能熵源模块                               |
| MBEDTLS_CTR_DRBG_C                 | 使能随机数模块                             |
| MBEDTLS_SHA256_C                   | 使能SHA256算法                             |
| MBEDTLS_MD_C                       | 开启MD通用接口                             |
| MBEDTLS_AES_ROM_TABLES             | 使能预定义S盒（节约内存空间）              |
| MBEDTLS_BIGNUM_C                   | 开启大数运算                               |
| MBEDTLS_GENPRIME                   | 开启生成素数                               |
| MBEDTLS_OID_C                      | 开启OID数据结构模块                        |
| MBEDTLS_RSA_C                      | 开启RSA算法                                |
| MBEDTLS_PKCS1_V21                  | 开启PKCS#1 v2.1填充方案（OAEP）            |

下面补充几个第一次出现宏的定义。

① **MBEDTLS_BIGNUM_C**

```c
/**
 * \def MBEDTLS_BIGNUM_C
 *
 * Enable the multi-precision integer library.
 *
 * Module:  library/bignum.c
 * Caller:  library/dhm.c
 *          library/ecp.c
 *          library/ecdsa.c
 *          library/rsa.c
 *          library/rsa_internal.c
 *          library/ssl_tls.c
 *
 * This module is required for RSA, DHM and ECC (ECDH, ECDSA) support.
 */
#define MBEDTLS_BIGNUM_C
```

② **MBEDTLS_GENPRIME**

```c
/**
 * \def MBEDTLS_GENPRIME
 *
 * Enable the prime-number generation code.
 *
 * Requires: MBEDTLS_BIGNUM_C
 */
#define MBEDTLS_GENPRIME
12345678
```

③ **MBEDTLS_OID_C**

```c
/**
 * \def MBEDTLS_OID_C
 *
 * Enable the OID database.
 *
 * Module:  library/oid.c
 * Caller:  library/asn1write.c
 *          library/pkcs5.c
 *          library/pkparse.c
 *          library/pkwrite.c
 *          library/rsa.c
 *          library/x509.c
 *          library/x509_create.c
 *          library/x509_crl.c
 *          library/x509_crt.c
 *          library/x509_csr.c
 *          library/x509write_crt.c
 *          library/x509write_csr.c
 *
 * This modules translates between OIDs and internal values.
 */
#define MBEDTLS_OID_C
```

④ **MBEDTLS_RSA_C**

```c
/**
 * \def MBEDTLS_RSA_C
 *
 * Enable the RSA public-key cryptosystem.
 *
 * Module:  library/rsa.c
 *          library/rsa_internal.c
 * Caller:  library/ssl_cli.c
 *          library/ssl_srv.c
 *          library/ssl_tls.c
 *          library/x509.c
 *
 * This module is used by the following key exchanges:
 *      RSA, DHE-RSA, ECDHE-RSA, RSA-PSK
 *
 * Requires: MBEDTLS_BIGNUM_C, MBEDTLS_OID_C
 */
#define MBEDTLS_RSA_C
```

⑤ **MBEDTLS_PKCS1_V21**

```c
/**
 * \def MBEDTLS_PKCS1_V21
 *
 * Enable support for PKCS#1 v2.1 encoding.
 *
 * Requires: MBEDTLS_MD_C, MBEDTLS_RSA_C
 *
 * This enables support for RSAES-OAEP and RSASSA-PSS operations.
 */
#define MBEDTLS_PKCS1_V21
```

编辑针对本实验的配置文件`mbedtls_config_rsa.h`：

```c
#ifndef _MBEDTLS_CONFIG_RSA_H_
#define _MBEDTLS_CONFIG_RSA_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_AES_C
#define MBEDTLS_AES_ROM_TABLES
#define MBEDTLS_CTR_DRBG_C
#define MBEDTLS_ENTROPY_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_MD_C
#define MBEDTLS_BIGNUM_C
#define MBEDTLS_GENPRIME
#define MBEDTLS_OID_C
#define MBEDTLS_RSA_C
#define MBEDTLS_PKCS1_V21

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_RSA_H_ */
```

在MDK配置mbedtls使用该配置文件：
![img](https://img-blog.csdnimg.cn/20200927110342915.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### RSA功能模块API说明

使用该功能模块需要包含头文件：

```c
#include "mbedtls/rsa.h"
```

① 初始化RSA结构体：

```c
/**
 * \brief          This function initializes an RSA context.
 *
 * \note           Set padding to #MBEDTLS_RSA_PKCS_V21 for the RSAES-OAEP
 *                 encryption scheme and the RSASSA-PSS signature scheme.
 *
 * \note           The \p hash_id parameter is ignored when using
 *                 #MBEDTLS_RSA_PKCS_V15 padding.
 *
 * \note           The choice of padding mode is strictly enforced for private key
 *                 operations, since there might be security concerns in
 *                 mixing padding modes. For public key operations it is
 *                 a default value, which can be overridden by calling specific
 *                 \c rsa_rsaes_xxx or \c rsa_rsassa_xxx functions.
 *
 * \note           The hash selected in \p hash_id is always used for OEAP
 *                 encryption. For PSS signatures, it is always used for
 *                 making signatures, but can be overridden for verifying them.
 *                 If set to #MBEDTLS_MD_NONE, it is always overridden.
 *
 * \param ctx      The RSA context to initialize. This must not be \c NULL.
 * \param padding  The padding mode to use. This must be either
 *                 #MBEDTLS_RSA_PKCS_V15 or #MBEDTLS_RSA_PKCS_V21.
 * \param hash_id  The hash identifier of ::mbedtls_md_type_t type, if
 *                 \p padding is #MBEDTLS_RSA_PKCS_V21. It is unused
 *                 otherwise.
 */
void mbedtls_rsa_init( mbedtls_rsa_context *ctx,
                       int padding,
                       int hash_id );
```

② RSA生成秘钥对：

```c
/**
 * \brief          This function generates an RSA keypair.
 *
 * \note           mbedtls_rsa_init() must be called before this function,
 *                 to set up the RSA context.
 *
 * \param ctx      The initialized RSA context used to hold the key.
 * \param f_rng    The RNG function to be used for key generation.
 *                 This must not be \c NULL.
 * \param p_rng    The RNG context to be passed to \p f_rng.
 *                 This may be \c NULL if \p f_rng doesn't need a context.
 * \param nbits    The size of the public key in bits.
 * \param exponent The public exponent to use. For example, \c 65537.
 *                 This must be odd and greater than \c 1.
 *
 * \return         \c 0 on success.
 * \return         An \c MBEDTLS_ERR_RSA_XXX error code on failure.
 */
int mbedtls_rsa_gen_key( mbedtls_rsa_context *ctx,
                         int (*f_rng)(void *, unsigned char *, size_t),
                         void *p_rng,
                         unsigned int nbits, int exponent );
```

③ RSA加密操作，通过参数指定公钥加密：

```c
/**
 * \brief          This function adds the message padding, then performs an RSA
 *                 operation.
 *
 *                 It is the generic wrapper for performing a PKCS#1 encryption
 *                 operation using the \p mode from the context.
 *
 * \deprecated     It is deprecated and discouraged to call this function
 *                 in #MBEDTLS_RSA_PRIVATE mode. Future versions of the library
 *                 are likely to remove the \p mode argument and have it
 *                 implicitly set to #MBEDTLS_RSA_PUBLIC.
 *
 * \note           Alternative implementations of RSA need not support
 *                 mode being set to #MBEDTLS_RSA_PRIVATE and might instead
 *                 return #MBEDTLS_ERR_PLATFORM_FEATURE_UNSUPPORTED.
 *
 * \param ctx      The initialized RSA context to use.
 * \param f_rng    The RNG to use. It is mandatory for PKCS#1 v2.1 padding
 *                 encoding, and for PKCS#1 v1.5 padding encoding when used
 *                 with \p mode set to #MBEDTLS_RSA_PUBLIC. For PKCS#1 v1.5
 *                 padding encoding and \p mode set to #MBEDTLS_RSA_PRIVATE,
 *                 it is used for blinding and should be provided in this
 *                 case; see mbedtls_rsa_private() for more.
 * \param p_rng    The RNG context to be passed to \p f_rng. May be
 *                 \c NULL if \p f_rng is \c NULL or if \p f_rng doesn't
 *                 need a context argument.
 * \param mode     The mode of operation. This must be either
 *                 #MBEDTLS_RSA_PUBLIC or #MBEDTLS_RSA_PRIVATE (deprecated).
 * \param ilen     The length of the plaintext in Bytes.
 * \param input    The input data to encrypt. This must be a readable
 *                 buffer of size \p ilen Bytes. It may be \c NULL if
 *                 `ilen == 0`.
 * \param output   The output buffer. This must be a writable buffer
 *                 of length \c ctx->len Bytes. For example, \c 256 Bytes
 *                 for an 2048-bit RSA modulus.
 *
 * \return         \c 0 on success.
 * \return         An \c MBEDTLS_ERR_RSA_XXX error code on failure.
 */
int mbedtls_rsa_pkcs1_encrypt( mbedtls_rsa_context *ctx,
                       int (*f_rng)(void *, unsigned char *, size_t),
                       void *p_rng,
                       int mode, size_t ilen,
                       const unsigned char *input,
                       unsigned char *output );
```

④ RSA解密操作，通过指定私钥解密：

```c
/**
 * \brief          This function performs an RSA operation, then removes the
 *                 message padding.
 *
 *                 It is the generic wrapper for performing a PKCS#1 decryption
 *                 operation using the \p mode from the context.
 *
 * \note           The output buffer length \c output_max_len should be
 *                 as large as the size \p ctx->len of \p ctx->N (for example,
 *                 128 Bytes if RSA-1024 is used) to be able to hold an
 *                 arbitrary decrypted message. If it is not large enough to
 *                 hold the decryption of the particular ciphertext provided,
 *                 the function returns \c MBEDTLS_ERR_RSA_OUTPUT_TOO_LARGE.
 *
 * \deprecated     It is deprecated and discouraged to call this function
 *                 in #MBEDTLS_RSA_PUBLIC mode. Future versions of the library
 *                 are likely to remove the \p mode argument and have it
 *                 implicitly set to #MBEDTLS_RSA_PRIVATE.
 *
 * \note           Alternative implementations of RSA need not support
 *                 mode being set to #MBEDTLS_RSA_PUBLIC and might instead
 *                 return #MBEDTLS_ERR_PLATFORM_FEATURE_UNSUPPORTED.
 *
 * \param ctx      The initialized RSA context to use.
 * \param f_rng    The RNG function. If \p mode is #MBEDTLS_RSA_PRIVATE,
 *                 this is used for blinding and should be provided; see
 *                 mbedtls_rsa_private() for more. If \p mode is
 *                 #MBEDTLS_RSA_PUBLIC, it is ignored.
 * \param p_rng    The RNG context to be passed to \p f_rng. This may be
 *                 \c NULL if \p f_rng is \c NULL or doesn't need a context.
 * \param mode     The mode of operation. This must be either
 *                 #MBEDTLS_RSA_PRIVATE or #MBEDTLS_RSA_PUBLIC (deprecated).
 * \param olen     The address at which to store the length of
 *                 the plaintext. This must not be \c NULL.
 * \param input    The ciphertext buffer. This must be a readable buffer
 *                 of length \c ctx->len Bytes. For example, \c 256 Bytes
 *                 for an 2048-bit RSA modulus.
 * \param output   The buffer used to hold the plaintext. This must
 *                 be a writable buffer of length \p output_max_len Bytes.
 * \param output_max_len The length in Bytes of the output buffer \p output.
 *
 * \return         \c 0 on success.
 * \return         An \c MBEDTLS_ERR_RSA_XXX error code on failure.
 */
int mbedtls_rsa_pkcs1_decrypt( mbedtls_rsa_context *ctx,
                       int (*f_rng)(void *, unsigned char *, size_t),
                       void *p_rng,
                       int mode, size_t *olen,
                       const unsigned char *input,
                       unsigned char *output,
                       size_t output_max_len );
```

⑤ 释放RSA结构体：

```c
/**
 * \brief          This function frees the components of an RSA key.
 *
 * \param ctx      The RSA context to free. May be \c NULL, in which case
 *                 this function is a no-op. If it is not \c NULL, it must
 *                 point to an initialized RSA context.
 */
void mbedtls_rsa_free( mbedtls_rsa_context *ctx );
```

### 编写测试函数

新建文件`mbedtls_rsa_test.c`，编写以下测试内容：

```c
/**
 * @brief   RSA Function demo
 * @author  mculover666
 * @date    2020/09/27
*/

#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_RSA_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/entropy.h"
#include "mbedtls/ctr_drbg.h"
#include "mbedtls/rsa.h"

char buf[516];

static void dump_rsa_key(mbedtls_rsa_context *ctx)
{
    size_t olen;
    
    printf("\n  +++++++++++++++++ rsa keypair +++++++++++++++++\n\n");
    mbedtls_mpi_write_string(&ctx->N , 16, buf, sizeof(buf), &olen);
    printf("N: %s\n", buf); 

    mbedtls_mpi_write_string(&ctx->E , 16, buf, sizeof(buf), &olen);
    printf("E: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->D , 16, buf, sizeof(buf), &olen);
    printf("D: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->P , 16, buf, sizeof(buf), &olen);
    printf("P: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->Q , 16, buf, sizeof(buf), &olen);
    printf("Q: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->DP, 16, buf, sizeof(buf), &olen);
    printf("DP: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->DQ, 16, buf, sizeof(buf), &olen);
    printf("DQ: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->QP, 16, buf, sizeof(buf), &olen);
    printf("QP: %s\n", buf);
    printf("\n  +++++++++++++++++ rsa keypair +++++++++++++++++\n\n");
}

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

uint8_t output_buf[2048/8];

int mbedtls_rsa_test(void)
{
    int ret;
    size_t olen;
    const char* msg = "HelloWorld";
    uint8_t decrypt_buf[20];
   
    const char *pers = "rsa_test";
    mbedtls_entropy_context entropy;
    mbedtls_ctr_drbg_context ctr_drbg;
    mbedtls_rsa_context ctx;

    /* 1. init structure */
    mbedtls_entropy_init(&entropy);
    mbedtls_ctr_drbg_init(&ctr_drbg);
    mbedtls_rsa_init(&ctx, MBEDTLS_RSA_PKCS_V21, MBEDTLS_MD_SHA256);
    
    /* 2. update seed with we own interface ported */
    printf( "\n  . Seeding the random number generator..." );
    
    ret = mbedtls_ctr_drbg_seed( &ctr_drbg, mbedtls_entropy_func, &entropy,
                               (const unsigned char *) pers,
                               strlen(pers));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ctr_drbg_seed returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );

    /* 3. generate an RSA keypair */
    printf( "\n  . Generate RSA keypair..." );
    
    ret = mbedtls_rsa_gen_key(&ctx, mbedtls_ctr_drbg_random, &ctr_drbg, 2048, 65537);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_rsa_gen_key returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* shwo RSA keypair */
    dump_rsa_key(&ctx);
    
    /* 4. encrypt */
    printf( "\n  . RSA pkcs1 encrypt..." );
    
    ret = mbedtls_rsa_pkcs1_encrypt(&ctx, mbedtls_ctr_drbg_random, &ctr_drbg, MBEDTLS_RSA_PUBLIC, 
                                    strlen(msg), (uint8_t *)msg, output_buf);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_rsa_pkcs1_encrypt returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show encrypt result */
    dump_buf(output_buf, sizeof(output_buf));
    
    /* 5. decrypt */
    printf( "\n  . RSA pkcs1 decrypt..." );
    
    ret = mbedtls_rsa_pkcs1_decrypt(&ctx, mbedtls_ctr_drbg_random, &ctr_drbg, MBEDTLS_RSA_PRIVATE, 
                                    &olen, output_buf, decrypt_buf, sizeof(decrypt_buf));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_rsa_pkcs1_decrypt returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show decrypt result */
    decrypt_buf[olen] = '\0';
    printf("decrypt result:[%s]\r\n", decrypt_buf);
    
    exit:
    
    /* 5. release structure */
    mbedtls_ctr_drbg_free(&ctr_drbg);
    mbedtls_entropy_free(&entropy);
    mbedtls_rsa_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_RSA_C */
```

### 测试结果

在 main.c 中声明该测试函数：

```c
extern int mbedtls_rsa_test(void);
```

然后在main函数中调用：

```c
/* 5. rsa test */
mbedtls_rsa_test();
```

### 堆内存空间的设置

**特别注意：**

mbedtls RSA算法中的生成秘钥对比较占用空间，再加上RSA算法计算过程涉及到大数运算，所以RSA算法对内存的消耗比较大。

mbedtls使用的是动态内存，从堆空间中分配，所以要在STM32启动文件中将堆空间设置的大一点：
![img](https://img-blog.csdnimg.cn/20200928201926696.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### 测试结果

编译、下载到开发板中，可以看到：

① 秘钥对生成结果（花费大概5min左右，主控芯片STM32L431主频为80Mhz）：
![img](https://img-blog.csdnimg.cn/20200927143614723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

② 使用公钥加密结果（因为RSA算法中的特性，所以同一份程序，同一份明文，每次重新运行生成的密文也不同）：
![img](https://img-blog.csdnimg.cn/20200927143637213.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
③ 使用私钥解密结果：
![img](https://img-blog.csdnimg.cn/20200927143743879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 六、DH秘钥协商算法的配置与使用

## DH秘钥协商算法

DH是一种秘钥协商算法，使得通信双方在不安全通道交换共享参数，从而协商出一个会话秘钥。

DH秘钥协商的过程如下：

① 通信双方A和B确认共享参数；
② A生成随机秘钥x；
③ B生成随机秘钥y；
④ A计算共享秘钥；
⑤ B计算共享秘钥；

DH秘钥协商算法可以有效防止中间窃听者，但是无法方式中间拦截者。

## DH秘钥协商功能的配置和使用

### 配置宏

使用DH秘钥协商功能需要提前开启伪随机数生成器（依赖AES算法、SHA256算法、MD通用接口），其中伪随机数生成器的硬件适配移植实现已经讲述，请参考对应的第二篇博客，不再赘述。

综合上述，本实验中需要开启的宏如下表：

| 宏定义                             | 功能                                       |
| ---------------------------------- | ------------------------------------------ |
| MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES | 不使用默认熵源**（如果已有、要屏蔽该宏）** |
| MBEDTLS_NO_PLATFORM_ENTROPY        | 不使用系统内置熵源                         |
| MBEDTLS_AES_C                      | 使用AES算法                                |
| MBEDTLS_ENTROPY_C                  | 使能熵源模块                               |
| MBEDTLS_CTR_DRBG_C                 | 使能随机数模块                             |
| MBEDTLS_SHA256_C                   | 使能SHA256算法                             |
| MBEDTLS_MD_C                       | 开启MD通用接口                             |
| MBEDTLS_AES_ROM_TABLES             | 使能预定义S盒（节约内存空间）              |
| MBEDTLS_BIGNUM_C                   | 开启大数运算                               |
| MBEDTLS_GENPRIME                   | 开启生成素数                               |
| MBEDTLS_DHM_C                      | 开启DH秘钥协商模块                         |

下面补充几个一个第一次出现宏的定义。

① **MBEDTLS_DHM_C**

```c
/**
 * \def MBEDTLS_DHM_C
 *
 * Enable the Diffie-Hellman-Merkle module.
 *
 * Module:  library/dhm.c
 * Caller:  library/ssl_cli.c
 *          library/ssl_srv.c
 *
 * This module is used by the following key exchanges:
 *      DHE-RSA, DHE-PSK
 *
 * \warning    Using DHE constitutes a security risk as it
 *             is not possible to validate custom DH parameters.
 *             If possible, it is recommended users should consider
 *             preferring other methods of key exchange.
 *             See dhm.h for more details.
 *
 */
#define MBEDTLS_DHM_C
```

编辑针对本实验的配置文件`mbedtls_config_dhm.h`：

```c
/**
 * @brief   Minimal configuration for DHM Function
 * @author  mculover666
 * @date    2020/09/28
*/

#ifndef _MBEDTLS_CONFIG_DHM_H_
#define _MBEDTLS_CONFIG_DHM_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_AES_C
#define MBEDTLS_AES_ROM_TABLES
#define MBEDTLS_CTR_DRBG_C
#define MBEDTLS_ENTROPY_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_MD_C
#define MBEDTLS_BIGNUM_C
#define MBEDTLS_GENPRIME
#define MBEDTLS_DHM_C

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_DHM_H_ */
```

在MDK配置mbedtls使用该配置文件：
![img](https://img-blog.csdnimg.cn/20200928201900998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### DH秘钥协商功能API说明

使用该功能模块需要包含头文件：

```c
#include "mbedtls/dhm.h"
```

① 初始化DH结构体：

```c
/**
 * \brief          This function initializes the DHM context.
 *
 * \param ctx      The DHM context to initialize.
 */
void mbedtls_dhm_init( mbedtls_dhm_context *ctx );
```

② 生成公开参数：

```c
/**
 * \brief          This function creates a DHM key pair and exports
 *                 the raw public key in big-endian format.
 *
 * \note           The destination buffer is always fully written
 *                 so as to contain a big-endian representation of G^X mod P.
 *                 If it is larger than \c ctx->len, it is padded accordingly
 *                 with zero-bytes at the beginning.
 *
 * \param ctx      The DHM context to use. This must be initialized and
 *                 have the DHM parameters set. It may or may not already
 *                 have imported the peer's public key.
 * \param x_size   The private key size in Bytes.
 * \param output   The destination buffer. This must be a writable buffer of
 *                 size \p olen Bytes.
 * \param olen     The length of the destination buffer. This must be at least
 *                 equal to `ctx->len` (the size of \c P).
 * \param f_rng    The RNG function. This must not be \c NULL.
 * \param p_rng    The RNG context to be passed to \p f_rng. This may be \c NULL
 *                 if \p f_rng doesn't need a context argument.
 *
 * \return         \c 0 on success.
 * \return         An \c MBEDTLS_ERR_DHM_XXX error code on failure.
 */
int mbedtls_dhm_make_public( mbedtls_dhm_context *ctx, int x_size,
                     unsigned char *output, size_t olen,
                     int (*f_rng)(void *, unsigned char *, size_t),
                     void *p_rng );
```

③ 读取公开参数：

```c
/**
 * \brief          This function imports the raw public value of the peer.
 *
 * \note           In a TLS handshake, this is the how the server imports
 *                 the Client's public DHM key.
 *
 * \param ctx      The DHM context to use. This must be initialized and have
 *                 its DHM parameters set, e.g. via mbedtls_dhm_set_group().
 *                 It may or may not already have generated its own private key.
 * \param input    The input buffer containing the \c G^Y value of the peer.
 *                 This must be a readable buffer of size \p ilen Bytes.
 * \param ilen     The size of the input buffer \p input in Bytes.
 *
 * \return         \c 0 on success.
 * \return         An \c MBEDTLS_ERR_DHM_XXX error code on failure.
 */
int mbedtls_dhm_read_public( mbedtls_dhm_context *ctx,
                     const unsigned char *input, size_t ilen );
```

④ 计算共享秘钥：

```c
/**
 * \brief          This function derives and exports the shared secret
 *                 \c (G^Y)^X mod \c P.
 *
 * \note           If \p f_rng is not \c NULL, it is used to blind the input as
 *                 a countermeasure against timing attacks. Blinding is used
 *                 only if our private key \c X is re-used, and not used
 *                 otherwise. We recommend always passing a non-NULL
 *                 \p f_rng argument.
 *
 * \param ctx           The DHM context to use. This must be initialized
 *                      and have its own private key generated and the peer's
 *                      public key imported.
 * \param output        The buffer to write the generated shared key to. This
 *                      must be a writable buffer of size \p output_size Bytes.
 * \param output_size   The size of the destination buffer. This must be at
 *                      least the size of \c ctx->len (the size of \c P).
 * \param olen          On exit, holds the actual number of Bytes written.
 * \param f_rng         The RNG function, for blinding purposes. This may
 *                      b \c NULL if blinding isn't needed.
 * \param p_rng         The RNG context. This may be \c NULL if \p f_rng
 *                      doesn't need a context argument.
 *
 * \return              \c 0 on success.
 * \return              An \c MBEDTLS_ERR_DHM_XXX error code on failure.
 */
int mbedtls_dhm_calc_secret( mbedtls_dhm_context *ctx,
                     unsigned char *output, size_t output_size, size_t *olen,
                     int (*f_rng)(void *, unsigned char *, size_t),
                     void *p_rng );
```

⑤ 释放DH结构体：

```c
/**
 * \brief          This function frees and clears the components
 *                 of a DHM context.
 *
 * \param ctx      The DHM context to free and clear. This may be \c NULL,
 *                 in which case this function is a no-op. If it is not \c NULL,
 *                 it must point to an initialized DHM context.
 */
void mbedtls_dhm_free( mbedtls_dhm_context *ctx );
```

⑥ 错误码：

```c
#define MBEDTLS_ERR_DHM_BAD_INPUT_DATA                    -0x3080  /**< Bad input parameters. */
#define MBEDTLS_ERR_DHM_READ_PARAMS_FAILED                -0x3100  /**< Reading of the DHM parameters failed. */
#define MBEDTLS_ERR_DHM_MAKE_PARAMS_FAILED                -0x3180  /**< Making of the DHM parameters failed. */
#define MBEDTLS_ERR_DHM_READ_PUBLIC_FAILED                -0x3200  /**< Reading of the public values failed. */
#define MBEDTLS_ERR_DHM_MAKE_PUBLIC_FAILED                -0x3280  /**< Making of the public value failed. */
#define MBEDTLS_ERR_DHM_CALC_SECRET_FAILED                -0x3300  /**< Calculation of the DHM secret failed. */
#define MBEDTLS_ERR_DHM_INVALID_FORMAT                    -0x3380  /**< The ASN.1 data is not formatted correctly. */
#define MBEDTLS_ERR_DHM_ALLOC_FAILED                      -0x3400  /**< Allocation of memory failed. */
#define MBEDTLS_ERR_DHM_FILE_IO_ERROR                     -0x3480  /**< Read or write of file failed. */

/* MBEDTLS_ERR_DHM_HW_ACCEL_FAILED is deprecated and should not be used. */
#define MBEDTLS_ERR_DHM_HW_ACCEL_FAILED                   -0x3500  /**< DHM hardware accelerator failed. */

#define MBEDTLS_ERR_DHM_SET_GROUP_FAILED                  -0x3580  /**< Setting the modulus and generator failed. */
```

### 编写测试函数

新建测试文件``，编写以下测试内容，其中服务器和客户端之间直接通信，没有采用网络通信：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_DHM_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/entropy.h"
#include "mbedtls/ctr_drbg.h"
#include "mbedtls/dhm.h"

#define GENERATOR   "2"
#define T_P          "FFFFFFFFFFFFFFFFADF85458A2BB4A9AAFDC5620273D3CF1D8B9C583CE2D3695" \
                     "A9E13641146433FBCC939DCE249B3EF97D2FE363630C75D8F681B202AEC4617A"\
                     "D3DF1ED5D5FD65612433F51F5F066ED0856365553DED1AF3B557135E7F57C935"\
                     "984F0C70E0E68B77E2A689DAF3EFE8721DF158A136ADE73530ACCA4F483A797A"\
                     "BC0AB182B324FB61D108A94BB2C8E3FBB96ADAB760D7F4681D4F42A3DE394DF4"\
                     "AE56EDE76372BB190B07A7C8EE0A6D709E02FCE1CDF7E2ECC03404CD28342F61"\
                     "9172FE9CE98583FF8E4F1232EEF28183C3FE3B1B4C6FAD733BB5FCBC2EC22005"\
                     "C58EF1837D1683B2C6F34A26C1B2EFFA886B423861285C97FFFFFFFFFFFFFFFF"

uint8_t server_pub[256];
uint8_t client_pub[256];
uint8_t server_secret[256];
uint8_t client_secret[256];

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

int mbedtls_dhm_test(void)
{
    int ret;
    size_t olen;
   
    const char *pers = "dhm_test";
    mbedtls_entropy_context entropy;
    mbedtls_ctr_drbg_context ctr_drbg;
    mbedtls_dhm_context dhm_server, dhm_client;

    /* 1. init structure */
    mbedtls_entropy_init(&entropy);
    mbedtls_ctr_drbg_init(&ctr_drbg);
    mbedtls_dhm_init(&dhm_server);
    mbedtls_dhm_init(&dhm_client);
    
    /* 2. update seed with we own interface ported */
    printf( "\n  . Seeding the random number generator..." );
    
    ret = mbedtls_ctr_drbg_seed( &ctr_drbg, mbedtls_entropy_func, &entropy,
                               (const unsigned char *) pers,
                               strlen(pers));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ctr_drbg_seed returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );

    /* 3. genetate 2048 bit prime(G, P) */
    printf("\n  . Genetate 2048 bit prime(G, P)...");
    
    mbedtls_mpi_read_string(&dhm_server.P, 16, T_P);
    mbedtls_mpi_read_string(&dhm_server.G, 10, GENERATOR);
    dhm_server.len = mbedtls_mpi_size(&dhm_server.P);
    
    mbedtls_mpi_read_string(&dhm_client.P, 16, T_P);
    mbedtls_mpi_read_string(&dhm_client.G, 10, GENERATOR);
    dhm_client.len = mbedtls_mpi_size(&dhm_client.P);
    
    printf("ok\r\n");
    
    /* 4. Server create a DHM key pair */
    printf("\n  . Server creates a DHM key pair...");
    
    ret = mbedtls_dhm_make_public(&dhm_server, 256, server_pub, sizeof(server_pub), mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_dhm_make_public returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    dump_buf(server_pub, sizeof(server_pub));
    
    /* 5. Client creates a DHM key pair */
    printf("\n  . Client create a DHM key pair...");
    
    ret = mbedtls_dhm_make_public(&dhm_client, 256, client_pub, sizeof(client_pub), mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_dhm_make_public returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    dump_buf(client_pub, sizeof(client_pub));
    
    /* 6. Server Read public key pair */
    printf("\n  . Server Read public key pair...");
    
    ret = mbedtls_dhm_read_public(&dhm_server, client_pub, sizeof(client_pub));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_dhm_read_public returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* 7. Client Read public key pair */
    printf("\n  . Client Read public key pair...");
    
    ret = mbedtls_dhm_read_public(&dhm_client, server_pub, sizeof(server_pub));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_dhm_read_public returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" ); 
    
    /* 8. calc the shared secret */
    printf("\n  . Server calc the shared secret...");
    
    ret = mbedtls_dhm_calc_secret(&dhm_server, server_secret, sizeof(server_secret), &olen, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_dhm_calc_secret returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" ); 
    dump_buf(server_secret, sizeof(server_secret));
    
    /* 9. calc the shared secret */
    printf("\n  . Client calc the shared secret...");
    
    ret = mbedtls_dhm_calc_secret(&dhm_client, client_secret, sizeof(client_secret), &olen, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_dhm_calc_secret returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" ); 
    dump_buf(client_secret, sizeof(client_secret));
    
    exit:
    
    /* 10. release structure */
    mbedtls_ctr_drbg_free(&ctr_drbg);
    mbedtls_entropy_free(&entropy);
    mbedtls_dhm_free(&dhm_server);
    mbedtls_dhm_free(&dhm_client);
    
    return ret;
}

#endif /* MBEDTLS_DHM_C */
```

### 测试结果

在 mian.c 中声明此测试函数：

```c
extern int mbedtls_dhm_test(void);
```

然后在main函数中调用测试函数：

```c
/* 6. dh test */
mbedtls_dhm_test();
```

因为本实验内存消耗较大，需要加大堆空间：
![img](https://img-blog.csdnimg.cn/20200929152619282.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
编译、下载、测试结果如下。

① 服务端生成的公开参数：
![img](https://img-blog.csdnimg.cn/20200929152724594.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
② 客户端生成的公开参数（**可以看到与服务端的公开参数不同**）：
![img](https://img-blog.csdnimg.cn/20200929152750286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
③ 服务端计算共享秘钥：
![img](https://img-blog.csdnimg.cn/20200929152846684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
④ 客户端计算共享秘钥（**可以看到与服务端相同**）：
![img](https://img-blog.csdnimg.cn/20200929152907206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 七、ECDH秘钥协商算法的配置与使用

## ECDH秘钥协商算法

ECDH是一种秘钥协商算法，使得通信双方在不安全通道交换共享参数，从而使用共享参数和自身私密参数计算出一个会话秘钥。

ECDH秘钥协商算法基于椭圆曲线密码系统（ECC），使用较短的秘钥长度可提供与RSA或DH算法同样的安全等级，**秘钥长度为160-256bit的椭圆曲线算法，与1024-3072bit的非ECC算法安全强度相同**。

ECDH秘钥协商的过程如下：

① 通信双方选择共同的椭圆曲线方程、相同的大素数P、相同的生成元G；

② 通信方A生成一个随机数作为自己的私钥，通过椭圆曲线标量乘法得到公开参数（公钥）；

③ 通信方B生成一个随机数作为自己的私钥，通过椭圆曲线标量乘法得到公开参数（公钥）；

④ 通信双方交换公钥，和自己的私钥一起计算得到共同的会话秘钥。

## ECDH秘钥协商功能的配置和使用

### 配置宏

使用ECDH秘钥协商功能需要提前开启伪随机数生成器（依赖AES算法、SHA256算法、MD通用接口），其中伪随机数生成器的硬件适配移植实现已经讲述，请参考对应的第二篇博客，不再赘述。

综合上述，本实验中需要开启的宏如下表：

| 宏定义                             | 功能                                       |
| ---------------------------------- | ------------------------------------------ |
| MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES | 不使用默认熵源**（如果已有、要屏蔽该宏）** |
| MBEDTLS_NO_PLATFORM_ENTROPY        | 不使用系统内置熵源                         |
| MBEDTLS_AES_C                      | 使用AES算法                                |
| MBEDTLS_ENTROPY_C                  | 使能熵源模块                               |
| MBEDTLS_CTR_DRBG_C                 | 使能随机数模块                             |
| MBEDTLS_SHA256_C                   | 使能SHA256算法                             |
| MBEDTLS_MD_C                       | 开启MD通用接口                             |
| MBEDTLS_AES_ROM_TABLES             | 使能预定义S盒（节约内存空间）              |
| MBEDTLS_BIGNUM_C                   | 开启大数运算                               |
| MBEDTLS_ECP_C                      | 开启椭圆曲线基础运算                       |
| MBEDTLS_ECDH_C                     | 开启椭圆曲线秘钥协商算法模块               |
| MBEDTLS_ECP_DP_SECP256R1_ENABLED   | 选择secp256r1曲线参数                      |

下面补充几个一个第一次出现宏的定义。

① **MBEDTLS_ECP_C**

```c
/**
 * \def MBEDTLS_ECP_C
 *
 * Enable the elliptic curve over GF(p) library.
 *
 * Module:  library/ecp.c
 * Caller:  library/ecdh.c
 *          library/ecdsa.c
 *          library/ecjpake.c
 *
 * Requires: MBEDTLS_BIGNUM_C and at least one MBEDTLS_ECP_DP_XXX_ENABLED
 */
#define MBEDTLS_ECP_C
```

② **MBEDTLS_ECDH_C**

```c
/**
 * \def MBEDTLS_ECDH_C
 *
 * Enable the elliptic curve Diffie-Hellman library.
 *
 * Module:  library/ecdh.c
 * Caller:  library/ssl_cli.c
 *          library/ssl_srv.c
 *
 * This module is used by the following key exchanges:
 *      ECDHE-ECDSA, ECDHE-RSA, DHE-PSK
 *
 * Requires: MBEDTLS_ECP_C
 */
#define MBEDTLS_ECDH_C
```

③ **MBEDTLS_ECP_DP_SECP256R1_ENABLED**（至少开启一种）

```c
/**
 * \def MBEDTLS_ECP_DP_SECP192R1_ENABLED
 *
 * MBEDTLS_ECP_XXXX_ENABLED: Enables specific curves within the Elliptic Curve
 * module.  By default all supported curves are enabled.
 *
 * Comment macros to disable the curve and functions for it
 */
//#define MBEDTLS_ECP_DP_SECP192R1_ENABLED
//#define MBEDTLS_ECP_DP_SECP224R1_ENABLED
#define MBEDTLS_ECP_DP_SECP256R1_ENABLED
//#define MBEDTLS_ECP_DP_SECP384R1_ENABLED
//#define MBEDTLS_ECP_DP_SECP521R1_ENABLED
//#define MBEDTLS_ECP_DP_SECP192K1_ENABLED
//#define MBEDTLS_ECP_DP_SECP224K1_ENABLED
//#define MBEDTLS_ECP_DP_SECP256K1_ENABLED
//#define MBEDTLS_ECP_DP_BP256R1_ENABLED
//#define MBEDTLS_ECP_DP_BP384R1_ENABLED
//#define MBEDTLS_ECP_DP_BP512R1_ENABLED
//#define MBEDTLS_ECP_DP_CURVE25519_ENABLED
//#define MBEDTLS_ECP_DP_CURVE448_ENABLED
```

编辑针对本实验的配置文件`mbedtls_config_ecdh.h`：

```c
#ifndef _MBEDTLS_CONFIG_ECDH_H_
#define _MBEDTLS_CONFIG_ECDH_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_AES_C
#define MBEDTLS_AES_ROM_TABLES
#define MBEDTLS_CTR_DRBG_C
#define MBEDTLS_ENTROPY_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_MD_C
#define MBEDTLS_BIGNUM_C
#define MBEDTLS_ECP_C
#define MBEDTLS_ECDH_C
#define MBEDTLS_ECP_DP_SECP256R1_ENABLED

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_ECDH_H_ */
```

### ECDH秘钥协商功能API说明

① 初始化椭圆曲线群结构体

```c
/**
 * \brief           This function initializes an ECP group context
 *                  without loading any domain parameters.
 *
 * \note            After this function is called, domain parameters
 *                  for various ECP groups can be loaded through the
 *                  mbedtls_ecp_group_load() or mbedtls_ecp_tls_read_group()
 *                  functions.
 */
void mbedtls_ecp_group_init( mbedtls_ecp_group *grp );
```

② 初始化椭圆曲线点结构体：

```c
/**
 * \brief           This function initializes an ECP group context
 *                  without loading any domain parameters.
 *
 * \note            After this function is called, domain parameters
 *                  for various ECP groups can be loaded through the
 *                  mbedtls_ecp_group_load() or mbedtls_ecp_tls_read_group()
 *                  functions.
 */
void mbedtls_ecp_group_init( mbedtls_ecp_group *grp );
```

③ 加载椭圆曲线：

```c
/**
 * \brief           This function sets up an ECP group context
 *                  from a standardized set of domain parameters.
 *
 * \note            The index should be a value of the NamedCurve enum,
 *                  as defined in <em>RFC-4492: Elliptic Curve Cryptography
 *                  (ECC) Cipher Suites for Transport Layer Security (TLS)</em>,
 *                  usually in the form of an \c MBEDTLS_ECP_DP_XXX macro.
 *
 * \param grp       The group context to setup. This must be initialized.
 * \param id        The identifier of the domain parameter set to load.
 *
 * \return          \c 0 on success.
 * \return          #MBEDTLS_ERR_ECP_FEATURE_UNAVAILABLE if \p id doesn't
 *                  correspond to a known group.
 * \return          Another negative error code on other kinds of failure.
 */
int mbedtls_ecp_group_load( mbedtls_ecp_group *grp, mbedtls_ecp_group_id id );
```

④ 生成公开参数：

```c
/**
 * \brief           This function generates an ECDH keypair on an elliptic
 *                  curve.
 *
 *                  This function performs the first of two core computations
 *                  implemented during the ECDH key exchange. The second core
 *                  computation is performed by mbedtls_ecdh_compute_shared().
 *
 * \see             ecp.h
 *
 * \param grp       The ECP group to use. This must be initialized and have
 *                  domain parameters loaded, for example through
 *                  mbedtls_ecp_load() or mbedtls_ecp_tls_read_group().
 * \param d         The destination MPI (private key).
 *                  This must be initialized.
 * \param Q         The destination point (public key).
 *                  This must be initialized.
 * \param f_rng     The RNG function to use. This must not be \c NULL.
 * \param p_rng     The RNG context to be passed to \p f_rng. This may be
 *                  \c NULL in case \p f_rng doesn't need a context argument.
 *
 * \return          \c 0 on success.
 * \return          Another \c MBEDTLS_ERR_ECP_XXX or
 *                  \c MBEDTLS_MPI_XXX error code on failure.
 */
int mbedtls_ecdh_gen_public( mbedtls_ecp_group *grp, mbedtls_mpi *d, mbedtls_ecp_point *Q,
                     int (*f_rng)(void *, unsigned char *, size_t),
                     void *p_rng );
```

⑤ 计算共享秘钥：

```c
/**
 * \brief           This function computes the shared secret.
 *
 *                  This function performs the second of two core computations
 *                  implemented during the ECDH key exchange. The first core
 *                  computation is performed by mbedtls_ecdh_gen_public().
 *
 * \see             ecp.h
 *
 * \note            If \p f_rng is not NULL, it is used to implement
 *                  countermeasures against side-channel attacks.
 *                  For more information, see mbedtls_ecp_mul().
 *
 * \param grp       The ECP group to use. This must be initialized and have
 *                  domain parameters loaded, for example through
 *                  mbedtls_ecp_load() or mbedtls_ecp_tls_read_group().
 * \param z         The destination MPI (shared secret).
 *                  This must be initialized.
 * \param Q         The public key from another party.
 *                  This must be initialized.
 * \param d         Our secret exponent (private key).
 *                  This must be initialized.
 * \param f_rng     The RNG function. This may be \c NULL if randomization
 *                  of intermediate results during the ECP computations is
 *                  not needed (discouraged). See the documentation of
 *                  mbedtls_ecp_mul() for more.
 * \param p_rng     The RNG context to be passed to \p f_rng. This may be
 *                  \c NULL if \p f_rng is \c NULL or doesn't need a
 *                  context argument.
 *
 * \return          \c 0 on success.
 * \return          Another \c MBEDTLS_ERR_ECP_XXX or
 *                  \c MBEDTLS_MPI_XXX error code on failure.
 */
int mbedtls_ecdh_compute_shared( mbedtls_ecp_group *grp, mbedtls_mpi *z,
                         const mbedtls_ecp_point *Q, const mbedtls_mpi *d,
                         int (*f_rng)(void *, unsigned char *, size_t),
                         void *p_rng );
```

⑥ 释放椭圆曲线群结构体：

```c
/**
 * \brief           This function frees the components of an ECP group.
 *
 * \param grp       The group to free. This may be \c NULL, in which
 *                  case this function returns immediately. If it is not
 *                  \c NULL, it must point to an initialized ECP group.
 */
void mbedtls_ecp_group_free( mbedtls_ecp_group *grp );
```

⑦ 释放椭圆曲线点结构体：

```c
/**
 * \brief           This function frees the components of a point.
 *
 * \param pt        The point to free.
 */
void mbedtls_ecp_point_free( mbedtls_ecp_point *pt );
```

⑧ 错误码（ECP计算部分）

```c
/*
 * ECP error codes
 */
#define MBEDTLS_ERR_ECP_BAD_INPUT_DATA                    -0x4F80  /**< Bad input parameters to function. */
#define MBEDTLS_ERR_ECP_BUFFER_TOO_SMALL                  -0x4F00  /**< The buffer is too small to write to. */
#define MBEDTLS_ERR_ECP_FEATURE_UNAVAILABLE               -0x4E80  /**< The requested feature is not available, for example, the requested curve is not supported. */
#define MBEDTLS_ERR_ECP_VERIFY_FAILED                     -0x4E00  /**< The signature is not valid. */
#define MBEDTLS_ERR_ECP_ALLOC_FAILED                      -0x4D80  /**< Memory allocation failed. */
#define MBEDTLS_ERR_ECP_RANDOM_FAILED                     -0x4D00  /**< Generation of random value, such as ephemeral key, failed. */
#define MBEDTLS_ERR_ECP_INVALID_KEY                       -0x4C80  /**< Invalid private or public key. */
#define MBEDTLS_ERR_ECP_SIG_LEN_MISMATCH                  -0x4C00  /**< The buffer contains a valid signature followed by more data. */

/* MBEDTLS_ERR_ECP_HW_ACCEL_FAILED is deprecated and should not be used. */
#define MBEDTLS_ERR_ECP_HW_ACCEL_FAILED                   -0x4B80  /**< The ECP hardware accelerator failed. */

#define MBEDTLS_ERR_ECP_IN_PROGRESS                       -0x4B00  /**< Operation in progress, call again with the same parameters to continue. */
```

### 编写测试函数

新建测试文件`mbedtls_ecdh_test.c`，编写以下测试内容，其中服务器和客户端之间直接通信，没有采用网络通信：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_ECDH_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/entropy.h"
#include "mbedtls/ctr_drbg.h"
#include "mbedtls/ecdh.h"

#define GENERATOR   "2"
#define T_P          "FFFFFFFFFFFFFFFFADF85458A2BB4A9AAFDC5620273D3CF1D8B9C583CE2D3695" \
                     "A9E13641146433FBCC939DCE249B3EF97D2FE363630C75D8F681B202AEC4617A"\
                     "D3DF1ED5D5FD65612433F51F5F066ED0856365553DED1AF3B557135E7F57C935"\
                     "984F0C70E0E68B77E2A689DAF3EFE8721DF158A136ADE73530ACCA4F483A797A"\
                     "BC0AB182B324FB61D108A94BB2C8E3FBB96ADAB760D7F4681D4F42A3DE394DF4"\
                     "AE56EDE76372BB190B07A7C8EE0A6D709E02FCE1CDF7E2ECC03404CD28342F61"\
                     "9172FE9CE98583FF8E4F1232EEF28183C3FE3B1B4C6FAD733BB5FCBC2EC22005"\
                     "C58EF1837D1683B2C6F34A26C1B2EFFA886B423861285C97FFFFFFFFFFFFFFFF"

uint8_t buf[65];

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

int mbedtls_ecdh_test(void)
{
    int ret;
    size_t olen;
   
    const char *pers = "ecdh_test";
    mbedtls_entropy_context entropy;
    mbedtls_ctr_drbg_context ctr_drbg;
    mbedtls_ecp_point client_pub, server_pub;
    mbedtls_ecp_group grp;
    mbedtls_mpi client_secret, server_secret;
    mbedtls_mpi client_pri, server_pri;
        
    /* 1. init structure */
    mbedtls_entropy_init(&entropy);
    mbedtls_ctr_drbg_init(&ctr_drbg);
    mbedtls_mpi_init(&client_secret);
    mbedtls_mpi_init(&server_secret);
    mbedtls_mpi_init(&client_pri);
    mbedtls_mpi_init(&server_pri);
    mbedtls_ecp_group_init(&grp);
    mbedtls_ecp_point_init(&client_pub);
    mbedtls_ecp_point_init(&server_pub);
    
    /* 2. update seed with we own interface ported */
    printf( "\n  . Seeding the random number generator..." );
    
    ret = mbedtls_ctr_drbg_seed( &ctr_drbg, mbedtls_entropy_func, &entropy,
                               (const unsigned char *) pers,
                               strlen(pers));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ctr_drbg_seed returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );

    /* 3.  select ecp group SECP256R1 */
    printf("\n  . Select ecp group SECP256R1...");
    
    ret = mbedtls_ecp_group_load(&grp, MBEDTLS_ECP_DP_SECP256R1);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecp_group_load returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    
    printf("ok\r\n");
    
    /* 4. Client generate public parameter */
    printf("\n  . Client Generate public parameter...");
    
    ret = mbedtls_ecdh_gen_public(&grp, &client_pri, &client_pub, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdh_gen_public returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show public parameter */
    mbedtls_ecp_point_write_binary(&grp, &client_pub, MBEDTLS_ECP_PF_UNCOMPRESSED, &olen, buf, sizeof(buf));
    dump_buf(buf, olen);
    
    /* 5. Client generate public parameter */
    printf("\n  . Server Generate public parameter...");
    
    ret = mbedtls_ecdh_gen_public(&grp, &server_pri, &server_pub, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdh_gen_public returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show public parameter */
    mbedtls_ecp_point_write_binary(&grp, &server_pub, MBEDTLS_ECP_PF_UNCOMPRESSED, &olen, buf, sizeof(buf));
    dump_buf(buf, olen);
    
    /* 6. Calc shared secret */
    printf("\n  . Client Calc shared secret...");
    
    ret = mbedtls_ecdh_compute_shared(&grp, &client_secret, &server_pub, &client_pri, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdh_compute_shared returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show public parameter */
    mbedtls_mpi_write_binary(&client_secret, buf, sizeof(buf));
    dump_buf(buf, olen);
    
    /* 7. Server Calc shared secret */
    printf("\n  . Server Calc shared secret...");
    
    ret = mbedtls_ecdh_compute_shared(&grp, &server_secret, &client_pub, &server_pri, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdh_compute_shared returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show public parameter */
    mbedtls_mpi_write_binary(&server_secret, buf, sizeof(buf));
    dump_buf(buf, olen);
    
    /* 8. mpi compare */
    ret = mbedtls_mpi_cmp_mpi(&server_secret, &client_secret);
    printf("compare result: %d\r\n", ret);
    
    exit:
    
    /* 10. release structure */
    mbedtls_ctr_drbg_free(&ctr_drbg);
    mbedtls_entropy_free(&entropy);
    mbedtls_mpi_free(&client_secret);
    mbedtls_mpi_free(&server_secret);
    mbedtls_mpi_free(&client_pri);
    mbedtls_mpi_free(&server_pri);
    mbedtls_ecp_group_free(&grp);
    mbedtls_ecp_point_free(&client_pub);
    mbedtls_ecp_point_free(&server_pub);
    
    return ret;
}

#endif /* MBEDTLS_DHM_C */
```

### 调用测试函数

在 main.c 中声明：

```c
extern int mbedtls_ecdh_test(void);
```

在 main 函数中调用此测试函数：

```c
/* 7. ecdh test */
mbedtls_ecdh_test();
```

编译、下载、在串口助手查看结果：
![img](https://img-blog.csdnimg.cn/20200930161558673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 八、数字签名算法的配置与使用（RSA数字签名算法、ECDSA数字签名算法）

## 数字签名算法

### 什么是数字签名

数字签名类似于盖章和签字，数字签名可以检查消息是否被篡改、并验证消息的可靠性，因为私钥只有签名者持有，所以还可以防止否认。

### 数字签名算法

① **RSA数字签名**

RSA数字签名算法基于RSA非对称加密算法，在用于数字签名时公钥和私钥的用法刚好相反：

- 发送方使用私钥对消息执行加密操作 = 对消息进行签名；
- 接收方使用公钥对签名进行解密 = 对签名进行认证；

RSA签名算法还需要包括填充方法，有两种：PKCS1-V1.5和PSS（使用随机数填充，**推荐使用**）。

② **DSA算法**

③ **ECDSA算法**

在相同的安全等级下，ECDSA算法具有秘钥短、执行效率高的特点，更适合物联网应用。

## RSA数字签名功能模块的配置与使用

### 配置宏

使用RSA功能需要提前开启伪随机数生成器（依赖AES算法、SHA256算法、MD通用接口），其中伪随机数生成器的硬件适配移植实现已经讲述，请参考对应的第二篇博客，不再赘述。

综合上述，本实验中需要开启的宏如下表：

| 宏定义                             | 功能                                       |
| ---------------------------------- | ------------------------------------------ |
| MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES | 不使用默认熵源**（如果已有、要屏蔽该宏）** |
| MBEDTLS_NO_PLATFORM_ENTROPY        | 不使用系统内置熵源                         |
| MBEDTLS_AES_C                      | 使用AES算法                                |
| MBEDTLS_ENTROPY_C                  | 使能熵源模块                               |
| MBEDTLS_CTR_DRBG_C                 | 使能随机数模块                             |
| MBEDTLS_SHA256_C                   | 使能SHA256算法                             |
| MBEDTLS_MD_C                       | 开启MD通用接口                             |
| MBEDTLS_AES_ROM_TABLES             | 使能预定义S盒（节约内存空间）              |
| MBEDTLS_BIGNUM_C                   | 开启大数运算                               |
| MBEDTLS_GENPRIME                   | 开启生成素数                               |
| MBEDTLS_OID_C                      | 开启OID数据结构模块                        |
| MBEDTLS_RSA_C                      | 开启RSA算法                                |
| MBEDTLS_PKCS1_V21                  | 开启PKCS#1 v2.1填充方案（OAEP）            |

新建配置文件`mbedtls_config_rsa_sign.h`，编写以下内容：

```c
#ifndef _MBEDTLS_CONFIG_RSA_SIGN_H_
#define _MBEDTLS_CONFIG_RSA_SIGN_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_AES_C
#define MBEDTLS_AES_ROM_TABLES
#define MBEDTLS_CTR_DRBG_C
#define MBEDTLS_ENTROPY_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_MD_C
#define MBEDTLS_BIGNUM_C
#define MBEDTLS_GENPRIME
#define MBEDTLS_OID_C
#define MBEDTLS_RSA_C
#define MBEDTLS_PKCS1_V21

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_RSA_SIGN_H_ */
```

在MDK中设置使用该配置文件：
![img](https://img-blog.csdnimg.cn/20201003110740288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### RSA数字签名功能模块API说明

使用该功能模块需要包含头文件：

```c
#include "mbedtls/rsa.h"
```

① RSA签名接口

```c
/**
 * \brief          This function performs a private RSA operation to sign
 *                 a message digest using PKCS#1.
 *
 *                 It is the generic wrapper for performing a PKCS#1
 *                 signature using the \p mode from the context.
 *
 * \note           The \p sig buffer must be as large as the size
 *                 of \p ctx->N. For example, 128 Bytes if RSA-1024 is used.
 *
 * \note           For PKCS#1 v2.1 encoding, see comments on
 *                 mbedtls_rsa_rsassa_pss_sign() for details on
 *                 \p md_alg and \p hash_id.
 *
 * \deprecated     It is deprecated and discouraged to call this function
 *                 in #MBEDTLS_RSA_PUBLIC mode. Future versions of the library
 *                 are likely to remove the \p mode argument and have it
 *                 implicitly set to #MBEDTLS_RSA_PRIVATE.
 *
 * \note           Alternative implementations of RSA need not support
 *                 mode being set to #MBEDTLS_RSA_PUBLIC and might instead
 *                 return #MBEDTLS_ERR_PLATFORM_FEATURE_UNSUPPORTED.
 *
 * \param ctx      The initialized RSA context to use.
 * \param f_rng    The RNG function to use. If the padding mode is PKCS#1 v2.1,
 *                 this must be provided. If the padding mode is PKCS#1 v1.5 and
 *                 \p mode is #MBEDTLS_RSA_PRIVATE, it is used for blinding
 *                 and should be provided; see mbedtls_rsa_private() for more
 *                 more. It is ignored otherwise.
 * \param p_rng    The RNG context to be passed to \p f_rng. This may be \c NULL
 *                 if \p f_rng is \c NULL or doesn't need a context argument.
 * \param mode     The mode of operation. This must be either
 *                 #MBEDTLS_RSA_PRIVATE or #MBEDTLS_RSA_PUBLIC (deprecated).
 * \param md_alg   The message-digest algorithm used to hash the original data.
 *                 Use #MBEDTLS_MD_NONE for signing raw data.
 * \param hashlen  The length of the message digest.
 *                 Ths is only used if \p md_alg is #MBEDTLS_MD_NONE.
 * \param hash     The buffer holding the message digest or raw data.
 *                 If \p md_alg is #MBEDTLS_MD_NONE, this must be a readable
 *                 buffer of length \p hashlen Bytes. If \p md_alg is not
 *                 #MBEDTLS_MD_NONE, it must be a readable buffer of length
 *                 the size of the hash corresponding to \p md_alg.
 * \param sig      The buffer to hold the signature. This must be a writable
 *                 buffer of length \c ctx->len Bytes. For example, \c 256 Bytes
 *                 for an 2048-bit RSA modulus. A buffer length of
 *                 #MBEDTLS_MPI_MAX_SIZE is always safe.
 *
 * \return         \c 0 if the signing operation was successful.
 * \return         An \c MBEDTLS_ERR_RSA_XXX error code on failure.
 */
int mbedtls_rsa_pkcs1_sign( mbedtls_rsa_context *ctx,
                    int (*f_rng)(void *, unsigned char *, size_t),
                    void *p_rng,
                    int mode,
                    mbedtls_md_type_t md_alg,
                    unsigned int hashlen,
                    const unsigned char *hash,
                    unsigned char *sig );
```

② RSA验证签名接口

```c
/**
 * \brief          This function performs a public RSA operation and checks
 *                 the message digest.
 *
 *                 This is the generic wrapper for performing a PKCS#1
 *                 verification using the mode from the context.
 *
 * \note           For PKCS#1 v2.1 encoding, see comments on
 *                 mbedtls_rsa_rsassa_pss_verify() about \p md_alg and
 *                 \p hash_id.
 *
 * \deprecated     It is deprecated and discouraged to call this function
 *                 in #MBEDTLS_RSA_PRIVATE mode. Future versions of the library
 *                 are likely to remove the \p mode argument and have it
 *                 set to #MBEDTLS_RSA_PUBLIC.
 *
 * \note           Alternative implementations of RSA need not support
 *                 mode being set to #MBEDTLS_RSA_PRIVATE and might instead
 *                 return #MBEDTLS_ERR_PLATFORM_FEATURE_UNSUPPORTED.
 *
 * \param ctx      The initialized RSA public key context to use.
 * \param f_rng    The RNG function to use. If \p mode is #MBEDTLS_RSA_PRIVATE,
 *                 this is used for blinding and should be provided; see
 *                 mbedtls_rsa_private() for more. Otherwise, it is ignored.
 * \param p_rng    The RNG context to be passed to \p f_rng. This may be
 *                 \c NULL if \p f_rng is \c NULL or doesn't need a context.
 * \param mode     The mode of operation. This must be either
 *                 #MBEDTLS_RSA_PUBLIC or #MBEDTLS_RSA_PRIVATE (deprecated).
 * \param md_alg   The message-digest algorithm used to hash the original data.
 *                 Use #MBEDTLS_MD_NONE for signing raw data.
 * \param hashlen  The length of the message digest.
 *                 This is only used if \p md_alg is #MBEDTLS_MD_NONE.
 * \param hash     The buffer holding the message digest or raw data.
 *                 If \p md_alg is #MBEDTLS_MD_NONE, this must be a readable
 *                 buffer of length \p hashlen Bytes. If \p md_alg is not
 *                 #MBEDTLS_MD_NONE, it must be a readable buffer of length
 *                 the size of the hash corresponding to \p md_alg.
 * \param sig      The buffer holding the signature. This must be a readable
 *                 buffer of length \c ctx->len Bytes. For example, \c 256 Bytes
 *                 for an 2048-bit RSA modulus.
 *
 * \return         \c 0 if the verify operation was successful.
 * \return         An \c MBEDTLS_ERR_RSA_XXX error code on failure.
 */
int mbedtls_rsa_pkcs1_verify( mbedtls_rsa_context *ctx,
                      int (*f_rng)(void *, unsigned char *, size_t),
                      void *p_rng,
                      int mode,
                      mbedtls_md_type_t md_alg,
                      unsigned int hashlen,
                      const unsigned char *hash,
                      const unsigned char *sig );
```

### 编写测试函数

新建文件`mbedtls_rsa_sign_test.c`，编写以下测试内容：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_RSA_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/entropy.h"
#include "mbedtls/ctr_drbg.h"
#include "mbedtls/rsa.h"

static char buf[516];

static void dump_rsa_key(mbedtls_rsa_context *ctx)
{
    size_t olen;
    
    printf("\n  +++++++++++++++++ rsa keypair +++++++++++++++++\n\n");
    mbedtls_mpi_write_string(&ctx->N , 16, buf, sizeof(buf), &olen);
    printf("N: %s\n", buf); 

    mbedtls_mpi_write_string(&ctx->E , 16, buf, sizeof(buf), &olen);
    printf("E: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->D , 16, buf, sizeof(buf), &olen);
    printf("D: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->P , 16, buf, sizeof(buf), &olen);
    printf("P: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->Q , 16, buf, sizeof(buf), &olen);
    printf("Q: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->DP, 16, buf, sizeof(buf), &olen);
    printf("DP: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->DQ, 16, buf, sizeof(buf), &olen);
    printf("DQ: %s\n", buf);

    mbedtls_mpi_write_string(&ctx->QP, 16, buf, sizeof(buf), &olen);
    printf("QP: %s\n", buf);
    printf("\n  +++++++++++++++++ rsa keypair +++++++++++++++++\n\n");
}

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

static uint8_t output_buf[2048/8];

int mbedtls_rsa_sign_test(void)
{
    int ret;
    const char* msg = "HelloWorld";
   
    const char *pers = "rsa_sign_test";
    mbedtls_entropy_context entropy;
    mbedtls_ctr_drbg_context ctr_drbg;
    mbedtls_rsa_context ctx;

    /* 1. init structure */
    mbedtls_entropy_init(&entropy);
    mbedtls_ctr_drbg_init(&ctr_drbg);
    mbedtls_rsa_init(&ctx, MBEDTLS_RSA_PKCS_V21, MBEDTLS_MD_SHA256);
    
    /* 2. update seed with we own interface ported */
    printf( "\n  . Seeding the random number generator..." );
    
    ret = mbedtls_ctr_drbg_seed( &ctr_drbg, mbedtls_entropy_func, &entropy,
                               (const unsigned char *) pers,
                               strlen(pers));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ctr_drbg_seed returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );

    /* 3. generate an RSA keypair */
    printf( "\n  . Generate RSA keypair..." );
    
    ret = mbedtls_rsa_gen_key(&ctx, mbedtls_ctr_drbg_random, &ctr_drbg, 2048, 65537);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_rsa_gen_key returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* shwo RSA keypair */
    dump_rsa_key(&ctx);
    
    /* 4. sign */
    printf( "\n  . RSA pkcs1 sign..." );
    
    ret = mbedtls_rsa_pkcs1_sign(&ctx, mbedtls_ctr_drbg_random, &ctr_drbg, MBEDTLS_RSA_PRIVATE, MBEDTLS_MD_SHA256, strlen(msg), (uint8_t *)msg, output_buf);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_rsa_pkcs1_sign returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show sign result */
    dump_buf(output_buf, sizeof(output_buf));
    
    /* 5. verify sign*/
    printf( "\n  . RSA pkcs1 verify..." );
    
    ret = mbedtls_rsa_pkcs1_verify(&ctx, mbedtls_ctr_drbg_random, &ctr_drbg, MBEDTLS_RSA_PUBLIC, MBEDTLS_MD_SHA256, strlen(msg), (uint8_t *)msg, output_buf);
    
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_rsa_pkcs1_encrypt returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    exit:
    
    /* 5. release structure */
    mbedtls_ctr_drbg_free(&ctr_drbg);
    mbedtls_entropy_free(&entropy);
    mbedtls_rsa_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_RSA_C */
```

### 测试结果

在main.c中声明该测试函数：

```c
extern int mbedtls_rsa_sign_test(void);
```

在main函数中调用该测试函数：

```c
/* 8. rsa sign test */
mbedtls_rsa_sign_test();
```

编译、下载，在串口助手中查看测试结果：

① 生成秘钥对成功（需要几min的时间）：
![img](https://img-blog.csdnimg.cn/20201003113344994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
② 生成签名和验证签名成功：
![img](https://img-blog.csdnimg.cn/20201003113416619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

## ECDSA数字签名功能模块的配置与使用

### 配置宏

使用ECDSA秘钥协商功能需要提前开启伪随机数生成器（依赖AES算法、SHA256算法、MD通用接口），其中伪随机数生成器的硬件适配移植实现已经讲述，请参考对应的第二篇博客，不再赘述。

综合上述，本实验中需要开启的宏如下表：

| 宏定义                             | 功能                                       |
| ---------------------------------- | ------------------------------------------ |
| MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES | 不使用默认熵源**（如果已有、要屏蔽该宏）** |
| MBEDTLS_NO_PLATFORM_ENTROPY        | 不使用系统内置熵源                         |
| MBEDTLS_AES_C                      | 使用AES算法                                |
| MBEDTLS_ENTROPY_C                  | 使能熵源模块                               |
| MBEDTLS_CTR_DRBG_C                 | 使能随机数模块                             |
| MBEDTLS_SHA256_C                   | 使能SHA256算法                             |
| MBEDTLS_MD_C                       | 开启MD通用接口                             |
| MBEDTLS_AES_ROM_TABLES             | 使能预定义S盒（节约内存空间）              |
| MBEDTLS_BIGNUM_C                   | 开启大数运算                               |
| MBEDTLS_ECP_C                      | 开启椭圆曲线基础运算                       |
| MBEDTLS_ECP_DP_SECP256R1_ENABLED   | 选择secp256r1曲线参数                      |
| MBEDTLS_ECDSA_C                    | 开启ECDSA算法                              |
| MBEDTLS_ASN1_WRITE_C               | 开启ASN1格式写入                           |
| MBEDTLS_ASN1_PARSE_C               | 开启ASN1结构解析                           |

下面补充几个一个第一次出现宏的定义。

① **MBEDTLS_ECDSA_C**

```c
/**
 * \def MBEDTLS_ECDSA_C
 *
 * Enable the elliptic curve DSA library.
 *
 * Module:  library/ecdsa.c
 * Caller:
 *
 * This module is used by the following key exchanges:
 *      ECDHE-ECDSA
 *
 * Requires: MBEDTLS_ECP_C, MBEDTLS_ASN1_WRITE_C, MBEDTLS_ASN1_PARSE_C
 */
#define MBEDTLS_ECDSA_C
```

② **MBEDTLS_ASN1_WRITE_C**

```c
/**
 * \def MBEDTLS_ASN1_WRITE_C
 *
 * Enable the generic ASN1 writer.
 *
 * Module:  library/asn1write.c
 * Caller:  library/ecdsa.c
 *          library/pkwrite.c
 *          library/x509_create.c
 *          library/x509write_crt.c
 *          library/x509write_csr.c
 */
#define MBEDTLS_ASN1_WRITE_C
```

③ **MBEDTLS_ASN1_PARSE_C**

```c
/**
 * \def MBEDTLS_ASN1_PARSE_C
 *
 * Enable the generic ASN1 parser.
 *
 * Module:  library/asn1.c
 * Caller:  library/x509.c
 *          library/dhm.c
 *          library/pkcs12.c
 *          library/pkcs5.c
 *          library/pkparse.c
 */
#define MBEDTLS_ASN1_PARSE_C
```

新建配置文件`mbedtls_config_ecdsa.h`，编写以下内容：

```c
#ifndef _MBEDTLS_CONFIG_ECDSA_H_
#define _MBEDTLS_CONFIG_ECDSA_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_AES_C
#define MBEDTLS_AES_ROM_TABLES
#define MBEDTLS_CTR_DRBG_C
#define MBEDTLS_ENTROPY_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_MD_C
#define MBEDTLS_BIGNUM_C
#define MBEDTLS_ECP_C
#define MBEDTLS_ECP_DP_SECP256R1_ENABLED
#define MBEDTLS_ECDSA_C
#define MBEDTLS_ASN1_WRITE_C
#define MBEDTLS_ASN1_PARSE_C

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_ECDSA_H_ */
```

在MDK中配置使用该配置文件：![img](https://img-blog.csdnimg.cn/20201003170538611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### ECDSA签名功能API说明

① 初始化ECDSA结构体：

```c
/**
 * \brief           This function initializes an ECDSA context.
 *
 * \param ctx       The ECDSA context to initialize.
 *                  This must not be \c NULL.
 */
void mbedtls_ecdsa_init( mbedtls_ecdsa_context *ctx );
```

② ECDSA生成秘钥接口：

```c
/**
 * \brief          This function generates an ECDSA keypair on the given curve.
 *
 * \see            ecp.h
 *
 * \param ctx      The ECDSA context to store the keypair in.
 *                 This must be initialized.
 * \param gid      The elliptic curve to use. One of the various
 *                 \c MBEDTLS_ECP_DP_XXX macros depending on configuration.
 * \param f_rng    The RNG function to use. This must not be \c NULL.
 * \param p_rng    The RNG context to be passed to \p f_rng. This may be
 *                 \c NULL if \p f_rng doesn't need a context argument.
 *
 * \return         \c 0 on success.
 * \return         An \c MBEDTLS_ERR_ECP_XXX code on failure.
 */
int mbedtls_ecdsa_genkey( mbedtls_ecdsa_context *ctx, mbedtls_ecp_group_id gid,
                  int (*f_rng)(void *, unsigned char *, size_t), void *p_rng );
```

③ ECDSA签名接口：

```c
/**
 * \brief           This function computes the ECDSA signature of a
 *                  previously-hashed message.
 *
 * \note            The deterministic version implemented in
 *                  mbedtls_ecdsa_sign_det() is usually preferred.
 *
 * \note            If the bitlength of the message hash is larger than the
 *                  bitlength of the group order, then the hash is truncated
 *                  as defined in <em>Standards for Efficient Cryptography Group
 *                  (SECG): SEC1 Elliptic Curve Cryptography</em>, section
 *                  4.1.3, step 5.
 *
 * \see             ecp.h
 *
 * \param grp       The context for the elliptic curve to use.
 *                  This must be initialized and have group parameters
 *                  set, for example through mbedtls_ecp_group_load().
 * \param r         The MPI context in which to store the first part
 *                  the signature. This must be initialized.
 * \param s         The MPI context in which to store the second part
 *                  the signature. This must be initialized.
 * \param d         The private signing key. This must be initialized.
 * \param buf       The content to be signed. This is usually the hash of
 *                  the original data to be signed. This must be a readable
 *                  buffer of length \p blen Bytes. It may be \c NULL if
 *                  \p blen is zero.
 * \param blen      The length of \p buf in Bytes.
 * \param f_rng     The RNG function. This must not be \c NULL.
 * \param p_rng     The RNG context to be passed to \p f_rng. This may be
 *                  \c NULL if \p f_rng doesn't need a context parameter.
 *
 * \return          \c 0 on success.
 * \return          An \c MBEDTLS_ERR_ECP_XXX
 *                  or \c MBEDTLS_MPI_XXX error code on failure.
 */
int mbedtls_ecdsa_sign( mbedtls_ecp_group *grp, mbedtls_mpi *r, mbedtls_mpi *s,
                const mbedtls_mpi *d, const unsigned char *buf, size_t blen,
                int (*f_rng)(void *, unsigned char *, size_t), void *p_rng );
```

④ ECDSA验证签名接口

```c
/**
 * \brief           This function verifies the ECDSA signature of a
 *                  previously-hashed message.
 *
 * \note            If the bitlength of the message hash is larger than the
 *                  bitlength of the group order, then the hash is truncated as
 *                  defined in <em>Standards for Efficient Cryptography Group
 *                  (SECG): SEC1 Elliptic Curve Cryptography</em>, section
 *                  4.1.4, step 3.
 *
 * \see             ecp.h
 *
 * \param grp       The ECP group to use.
 *                  This must be initialized and have group parameters
 *                  set, for example through mbedtls_ecp_group_load().
 * \param buf       The hashed content that was signed. This must be a readable
 *                  buffer of length \p blen Bytes. It may be \c NULL if
 *                  \p blen is zero.
 * \param blen      The length of \p buf in Bytes.
 * \param Q         The public key to use for verification. This must be
 *                  initialized and setup.
 * \param r         The first integer of the signature.
 *                  This must be initialized.
 * \param s         The second integer of the signature.
 *                  This must be initialized.
 *
 * \return          \c 0 on success.
 * \return          #MBEDTLS_ERR_ECP_BAD_INPUT_DATA if the signature
 *                  is invalid.
 * \return          An \c MBEDTLS_ERR_ECP_XXX or \c MBEDTLS_MPI_XXX
 *                  error code on failure for any other reason.
 */
int mbedtls_ecdsa_verify( mbedtls_ecp_group *grp,
                          const unsigned char *buf, size_t blen,
                          const mbedtls_ecp_point *Q, const mbedtls_mpi *r,
                          const mbedtls_mpi *s);
```

⑤ 释放ECDSA结构体：

```c
/**
 * \brief           This function frees an ECDSA context.
 *
 * \param ctx       The ECDSA context to free. This may be \c NULL,
 *                  in which case this function does nothing. If it
 *                  is not \c NULL, it must be initialized.
 */
void mbedtls_ecdsa_free( mbedtls_ecdsa_context *ctx );
```

### 编写测试函数

新建文件`mbedtls_ecdsa_test`，编写以下测试函数：

```c
#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_ECDSA_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/entropy.h"
#include "mbedtls/ctr_drbg.h"
#include "mbedtls/ecdsa.h"

uint8_t buf[97];

static void dump_buf(uint8_t *buf, uint32_t len)
{
    int i;
    
    for (i = 0; i < len; i++) {
        printf("%s%02X%s", i % 16 == 0 ? "\r\n\t" : " ", 
                           buf[i], 
                           i == len - 1 ? "\r\n" : "");
    }
}

int mbedtls_ecdsa_test(void)
{
    int ret;
    size_t qlen, dlen;
    size_t rlen, slen;
    uint8_t hash[32];
   
    const char *msg  = "HelloWorld";
    const char *pers = "ecdsa_test";
    mbedtls_entropy_context entropy;
    mbedtls_ctr_drbg_context ctr_drbg;
    mbedtls_mpi r, s;
    mbedtls_ecdsa_context ctx;
    mbedtls_md_context_t md_ctx;
        
    /* 1. init structure */
    mbedtls_md_init(&md_ctx);
    mbedtls_entropy_init(&entropy);
    mbedtls_ctr_drbg_init(&ctr_drbg);
    mbedtls_mpi_init(&r);
    mbedtls_mpi_init(&s);
    
    /* 2. update seed with we own interface ported */
    printf( "\n  . Seeding the random number generator..." );
    
    ret = mbedtls_ctr_drbg_seed( &ctr_drbg, mbedtls_entropy_func, &entropy,
                               (const unsigned char *) pers,
                               strlen(pers));
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ctr_drbg_seed returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* 3. hash message */
    printf( "\n  . Hash message..." );
    
    ret = mbedtls_md(mbedtls_md_info_from_type(MBEDTLS_MD_SHA256), (uint8_t *)msg, strlen(msg), hash);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_md returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show hash */
    dump_buf(hash, sizeof(hash));
    
    /* 4. generate keypair */
    printf( "\n  . Generate ecdsa keypair..." );
    
    ret = mbedtls_ecdsa_genkey(&ctx, MBEDTLS_ECP_DP_SECP256R1, mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdsa_genkey returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show keypair */
    mbedtls_ecp_point_write_binary(&ctx.grp, &ctx.Q, MBEDTLS_ECP_PF_UNCOMPRESSED, &qlen, buf, sizeof(buf));
    dlen = mbedtls_mpi_size(&ctx.d);
    mbedtls_mpi_write_binary(&ctx.d, buf + qlen, dlen);
    dump_buf(buf, qlen + dlen);
    
    /* 5. ecdsa sign */
    printf( "\n  . ECDSA sign..." );
    
    ret = mbedtls_ecdsa_sign(&ctx.grp, &r, &s, &ctx.d, hash, sizeof(hash), mbedtls_ctr_drbg_random, &ctr_drbg);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdsa_sign returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* show sign */
    rlen = mbedtls_mpi_size(&r);
    slen = mbedtls_mpi_size(&s);
    mbedtls_mpi_write_binary(&r, buf, rlen);
    mbedtls_mpi_write_binary(&s, buf + rlen, slen);
    dump_buf(buf, rlen + slen);
    
    /* 6. ecdsa verify */
    printf( "\n  . ECDSA verify..." );
    
    ret = mbedtls_ecdsa_verify(&ctx.grp, hash, sizeof(hash), &ctx.Q, &r, &s);
    if(ret != 0) {
        printf( " failed\n  ! mbedtls_ecdsa_verify returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );

    exit:
    
    /* 7. release structure */
    mbedtls_ctr_drbg_free(&ctr_drbg);
    mbedtls_entropy_free(&entropy);
    mbedtls_mpi_free(&r);
    mbedtls_mpi_free(&s);
    mbedtls_md_free(&md_ctx);
    mbedtls_ecdsa_free(&ctx);
    
    return ret;
}

#endif /* MBEDTLS_DHM_C */
```

### 测试结果

在main.c中声明该函数：

```c
extern int mbedtls_ecdsa_test(void);
```

在main函数中调用该函数：

```c
/* 9. ecdsa sign test */
mbedtls_ecdsa_test();
```

编译、下载，在串口助手中查看结果：
![img](https://img-blog.csdnimg.cn/20201003193818564.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

# 九、数字证书及 X.509 证书标准

## X.509证书标准

X.509是数字证书的一种标准格式，由国际电信联盟的标准化部分定义。

### X.509证书的结构

X.509证书主要包括12个字段，如下表：

| 名称                          | 字段           | 意义                                   |
| ----------------------------- | -------------- | -------------------------------------- |
| Version                       | 版本           | 证书版本                               |
| Serial number                 | 序列号         | 证书的唯一序列号                       |
| Signature                     | 签名算法       | CA对证书进行签名所使用的签名算法       |
| Issuer                        | 发行商名称     | 标识对证书进行签名并颁发的实体         |
| Validity                      | 有效期         | 标识证书的生效日期和终止日期           |
| Subject                       | 证书主体名称   | 标识获得证书的主体                     |
| Subject public key infomation | 公钥           | 用于指示使用者公钥信息                 |
| Issure unique ID              | 颁发者唯一标识 | 用于标识证书签发机构                   |
| subject unique ID             | 使用者唯一标识 | 用于标识证书使用者实体                 |
| Extensions                    | 扩展           | 一个或多个扩展域                       |
| signature Algorithm           | 签名算法       | 标识CA对证书签名所使用的签名算法和参数 |
| signature Value               | 签名值         | CA对证书的签名结果                     |

### 获取证书示例（百度）

下面以百度的证书为例讲解X.509证书标准。

使用浏览器访问百度首页：https://www.baidu.com/，点击域名旁边的【小绿锁】，点击【证书】。
![img](https://img-blog.csdnimg.cn/20201003204805665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
点击之后即可查看到百度的证书：
![img](https://img-blog.csdnimg.cn/20201003204907406.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
① 点击【证书路径】，将一级证书（根证书）导出：
![img](https://img-blog.csdnimg.cn/20201003205037671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
点击【详细信息】，将此证书内容【复制到文件】：
![、](https://img-blog.csdnimg.cn/20201003205127263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
进入证书导出向导：
![img](https://img-blog.csdnimg.cn/2020100320522449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
选择使用【Base64编码】导出：
![img](https://img-blog.csdnimg.cn/2020100320531961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
选择导出文件路径：
![img](https://img-blog.csdnimg.cn/20201003205427731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
导出成功：![img](https://img-blog.csdnimg.cn/20201003205524237.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
② 同样的方法，将二级证书导出为baidu_2.cer：
![img](https://img-blog.csdnimg.cn/20201003205629372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
③ 同样的方法，将三级证书导出为baidu_3.cer：
![img](https://img-blog.csdnimg.cn/20201003205710197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
三份证书如图：
![img](https://img-blog.csdnimg.cn/2020100320581770.png#pic_center)
使用记事本打开任意一份，可以看到该证书内容：![img](https://img-blog.csdnimg.cn/20201003205912844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
**新建一个空文件`baidu_ca.txt`，将三份内容按照次序复制到该文件中，后续使用**。
![img](https://img-blog.csdnimg.cn/20201003210126170.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### 查看百度证书内容

使用openssl工具查看刚刚获取的百度证书内容：

```bash
openssl x509 -text -in baidu_3.cer -noout
1
```

① 证书颁发者和使用者信息：
![img](https://img-blog.csdnimg.cn/20201004102053366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
② 公钥算法和公钥内容：
![img](https://img-blog.csdnimg.cn/20201004101257191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
③ 签名算法和内容：
![img](https://img-blog.csdnimg.cn/2020100410141193.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)
同样的方法可以查看百度二级证书和百度一级证书（根证书）的内容。

## X509证书解析验证功能的配置与使用

### 配置宏

①

```c
#define MBEDTLS_PK_C
```

②

```c
#define MBEDTLS_PK_PARSE_C
```

③

```c
#define MBEDTLS_ASN1_PARSE_C
```

④

```c
#define MBEDTLS_ASN1_WRITE_C
```

⑤

```c
#define MBEDTLS_X509_USE_C
```

⑥

```c
#define MBEDTLS_BASE64_C
```

⑦

```c
#define MBEDTLS_PEM_PARSE_C
```

⑧

```c
#define MBEDTLS_X509_CRT_PARSE_C
```

新建配置文件`mbedtls_config_x509.h`，编辑以下内容：

```c
#ifndef _MBEDTLS_CONFIG_X509_H_
#define _MBEDTLS_CONFIG_X509_H_

/* System support */
#define MBEDTLS_HAVE_ASM
//#define MBEDTLS_HAVE_TIME

/* mbed feature support */
#define MBEDTLS_ENTROPY_HARDWARE_ALT
//#define MBEDTLS_NO_DEFAULT_ENTROPY_SOURCES
#define MBEDTLS_NO_PLATFORM_ENTROPY

/* mbed modules */
#define MBEDTLS_SHA1_C
#define MBEDTLS_SHA256_C
#define MBEDTLS_MD_C
#define MBEDTLS_BIGNUM_C
#define MBEDTLS_OID_C
#define MBEDTLS_RSA_C
#define MBEDTLS_PKCS1_V21
#define MBEDTLS_PK_C
#define MBEDTLS_PK_PARSE_C
#define MBEDTLS_ASN1_PARSE_C
#define MBEDTLS_ASN1_WRITE_C
#define MBEDTLS_X509_USE_C
#define MBEDTLS_BASE64_C
#define MBEDTLS_PEM_PARSE_C
#define MBEDTLS_X509_CRT_PARSE_C

#include "mbedtls/check_config.h"

#endif /* _MBEDTLS_CONFIG_X509_H_ */
```

在MDK中配置使用该文件：
![img](https://img-blog.csdnimg.cn/20201004132201128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)

### API说明

使用时需要包含头文件：

```c
#include "mbedtls/x509_crt.h"
```

① 初始化证书结构体

```c
void mbedtls_x509_crt_init( mbedtls_x509_crt *crt );
```

② 证书解析

```c
/**
 * \brief          Parse one DER-encoded or one or more concatenated PEM-encoded
 *                 certificates and add them to the chained list.
 *
 *                 For CRTs in PEM encoding, the function parses permissively:
 *                 if at least one certificate can be parsed, the function
 *                 returns the number of certificates for which parsing failed
 *                 (hence \c 0 if all certificates were parsed successfully).
 *                 If no certificate could be parsed, the function returns
 *                 the first (negative) error encountered during parsing.
 *
 *                 PEM encoded certificates may be interleaved by other data
 *                 such as human readable descriptions of their content, as
 *                 long as the certificates are enclosed in the PEM specific
 *                 '-----{BEGIN/END} CERTIFICATE-----' delimiters.
 *
 * \param chain    The chain to which to add the parsed certificates.
 * \param buf      The buffer holding the certificate data in PEM or DER format.
 *                 For certificates in PEM encoding, this may be a concatenation
 *                 of multiple certificates; for DER encoding, the buffer must
 *                 comprise exactly one certificate.
 * \param buflen   The size of \p buf, including the terminating \c NULL byte
 *                 in case of PEM encoded data.
 *
 * \return         \c 0 if all certificates were parsed successfully.
 * \return         The (positive) number of certificates that couldn't
 *                 be parsed if parsing was partly successful (see above).
 * \return         A negative X509 or PEM error code otherwise.
 *
 */
int mbedtls_x509_crt_parse( mbedtls_x509_crt *chain, const unsigned char *buf, size_t buflen );
```

③ 获取证书信息

```c
/**
 * \brief          Returns an informational string about the
 *                 certificate.
 *
 * \param buf      Buffer to write to
 * \param size     Maximum size of buffer
 * \param prefix   A line prefix
 * \param crt      The X509 certificate to represent
 *
 * \return         The length of the string written (not including the
 *                 terminated nul byte), or a negative error code.
 */
int mbedtls_x509_crt_info( char *buf, size_t size, const char *prefix,
                   const mbedtls_x509_crt *crt );
```

④ 获取证书认证信息：

```c
/**
 * \brief          Returns an informational string about the
 *                 verification status of a certificate.
 *
 * \param buf      Buffer to write to
 * \param size     Maximum size of buffer
 * \param prefix   A line prefix
 * \param flags    Verification flags created by mbedtls_x509_crt_verify()
 *
 * \return         The length of the string written (not including the
 *                 terminated nul byte), or a negative error code.
 */
int mbedtls_x509_crt_verify_info( char *buf, size_t size, const char *prefix,
                          uint32_t flags );
```

⑤ 证书认证

```c
/**
 * \brief          Verify a chain of certificates.
 *
 *                 The verify callback is a user-supplied callback that
 *                 can clear / modify / add flags for a certificate. If set,
 *                 the verification callback is called for each
 *                 certificate in the chain (from the trust-ca down to the
 *                 presented crt). The parameters for the callback are:
 *                 (void *parameter, mbedtls_x509_crt *crt, int certificate_depth,
 *                 int *flags). With the flags representing current flags for
 *                 that specific certificate and the certificate depth from
 *                 the bottom (Peer cert depth = 0).
 *
 *                 All flags left after returning from the callback
 *                 are also returned to the application. The function should
 *                 return 0 for anything (including invalid certificates)
 *                 other than fatal error, as a non-zero return code
 *                 immediately aborts the verification process. For fatal
 *                 errors, a specific error code should be used (different
 *                 from MBEDTLS_ERR_X509_CERT_VERIFY_FAILED which should not
 *                 be returned at this point), or MBEDTLS_ERR_X509_FATAL_ERROR
 *                 can be used if no better code is available.
 *
 * \note           In case verification failed, the results can be displayed
 *                 using \c mbedtls_x509_crt_verify_info()
 *
 * \note           Same as \c mbedtls_x509_crt_verify_with_profile() with the
 *                 default security profile.
 *
 * \note           It is your responsibility to provide up-to-date CRLs for
 *                 all trusted CAs. If no CRL is provided for the CA that was
 *                 used to sign the certificate, CRL verification is skipped
 *                 silently, that is *without* setting any flag.
 *
 * \note           The \c trust_ca list can contain two types of certificates:
 *                 (1) those of trusted root CAs, so that certificates
 *                 chaining up to those CAs will be trusted, and (2)
 *                 self-signed end-entity certificates to be trusted (for
 *                 specific peers you know) - in that case, the self-signed
 *                 certificate doesn't need to have the CA bit set.
 *
 * \param crt      The certificate chain to be verified.
 * \param trust_ca The list of trusted CAs.
 * \param ca_crl   The list of CRLs for trusted CAs.
 * \param cn       The expected Common Name. This will be checked to be
 *                 present in the certificate's subjectAltNames extension or,
 *                 if this extension is absent, as a CN component in its
 *                 Subject name. Currently only DNS names are supported. This
 *                 may be \c NULL if the CN need not be verified.
 * \param flags    The address at which to store the result of the verification.
 *                 If the verification couldn't be completed, the flag value is
 *                 set to (uint32_t) -1.
 * \param f_vrfy   The verification callback to use. See the documentation
 *                 of mbedtls_x509_crt_verify() for more information.
 * \param p_vrfy   The context to be passed to \p f_vrfy.
 *
 * \return         \c 0 if the chain is valid with respect to the
 *                 passed CN, CAs, CRLs and security profile.
 * \return         #MBEDTLS_ERR_X509_CERT_VERIFY_FAILED in case the
 *                 certificate chain verification failed. In this case,
 *                 \c *flags will have one or more
 *                 \c MBEDTLS_X509_BADCERT_XXX or \c MBEDTLS_X509_BADCRL_XXX
 *                 flags set.
 * \return         Another negative error code in case of a fatal error
 *                 encountered during the verification process.
 */
int mbedtls_x509_crt_verify( mbedtls_x509_crt *crt,
                     mbedtls_x509_crt *trust_ca,
                     mbedtls_x509_crl *ca_crl,
                     const char *cn, uint32_t *flags,
                     int (*f_vrfy)(void *, mbedtls_x509_crt *, int, uint32_t *),
                     void *p_vrfy );
```

⑥ 释放证书结构体

```c
/**
 * \brief          Unallocate all certificate data
 *
 * \param crt      Certificate chain to free
 */
void mbedtls_x509_crt_free( mbedtls_x509_crt *crt );
```

⑦ 错误码：

```c
/**
 * \name X509 Error codes
 * \{
 */
#define MBEDTLS_ERR_X509_FEATURE_UNAVAILABLE              -0x2080  /**< Unavailable feature, e.g. RSA hashing/encryption combination. */
#define MBEDTLS_ERR_X509_UNKNOWN_OID                      -0x2100  /**< Requested OID is unknown. */
#define MBEDTLS_ERR_X509_INVALID_FORMAT                   -0x2180  /**< The CRT/CRL/CSR format is invalid, e.g. different type expected. */
#define MBEDTLS_ERR_X509_INVALID_VERSION                  -0x2200  /**< The CRT/CRL/CSR version element is invalid. */
#define MBEDTLS_ERR_X509_INVALID_SERIAL                   -0x2280  /**< The serial tag or value is invalid. */
#define MBEDTLS_ERR_X509_INVALID_ALG                      -0x2300  /**< The algorithm tag or value is invalid. */
#define MBEDTLS_ERR_X509_INVALID_NAME                     -0x2380  /**< The name tag or value is invalid. */
#define MBEDTLS_ERR_X509_INVALID_DATE                     -0x2400  /**< The date tag or value is invalid. */
#define MBEDTLS_ERR_X509_INVALID_SIGNATURE                -0x2480  /**< The signature tag or value invalid. */
#define MBEDTLS_ERR_X509_INVALID_EXTENSIONS               -0x2500  /**< The extension tag or value is invalid. */
#define MBEDTLS_ERR_X509_UNKNOWN_VERSION                  -0x2580  /**< CRT/CRL/CSR has an unsupported version number. */
#define MBEDTLS_ERR_X509_UNKNOWN_SIG_ALG                  -0x2600  /**< Signature algorithm (oid) is unsupported. */
#define MBEDTLS_ERR_X509_SIG_MISMATCH                     -0x2680  /**< Signature algorithms do not match. (see \c ::mbedtls_x509_crt sig_oid) */
#define MBEDTLS_ERR_X509_CERT_VERIFY_FAILED               -0x2700  /**< Certificate verification failed, e.g. CRL, CA or signature check failed. */
#define MBEDTLS_ERR_X509_CERT_UNKNOWN_FORMAT              -0x2780  /**< Format not recognized as DER or PEM. */
#define MBEDTLS_ERR_X509_BAD_INPUT_DATA                   -0x2800  /**< Input invalid. */
#define MBEDTLS_ERR_X509_ALLOC_FAILED                     -0x2880  /**< Allocation of memory failed. */
#define MBEDTLS_ERR_X509_FILE_IO_ERROR                    -0x2900  /**< Read/write of file failed. */
#define MBEDTLS_ERR_X509_BUFFER_TOO_SMALL                 -0x2980  /**< Destination buffer is too small. */
#define MBEDTLS_ERR_X509_FATAL_ERROR                      -0x3000  /**< A fatal error occurred, eg the chain is too long or the vrfy callback failed. */
```

### 编写测试函数

编写头文件`baidu_certs.h`，将百度的证书存储：

```c
#ifndef __CERTS_H__
#define __CERTS_H__

const char baidu_ca_cert[] =
"-----BEGIN CERTIFICATE-----\r\n"
"MIIKLjCCCRagAwIBAgIMclh4Nm6fVugdQYhIMA0GCSqGSIb3DQEBCwUAMGYxCzAJ\r\n"
"BgNVBAYTAkJFMRkwFwYDVQQKExBHbG9iYWxTaWduIG52LXNhMTwwOgYDVQQDEzNH\r\n"
"bG9iYWxTaWduIE9yZ2FuaXphdGlvbiBWYWxpZGF0aW9uIENBIC0gU0hBMjU2IC0g\r\n"
"RzIwHhcNMjAwNDAyMDcwNDU4WhcNMjEwNzI2MDUzMTAyWjCBpzELMAkGA1UEBhMC\r\n"
"Q04xEDAOBgNVBAgTB2JlaWppbmcxEDAOBgNVBAcTB2JlaWppbmcxJTAjBgNVBAsT\r\n"
"HHNlcnZpY2Ugb3BlcmF0aW9uIGRlcGFydG1lbnQxOTA3BgNVBAoTMEJlaWppbmcg\r\n"
"QmFpZHUgTmV0Y29tIFNjaWVuY2UgVGVjaG5vbG9neSBDby4sIEx0ZDESMBAGA1UE\r\n"
"AxMJYmFpZHUuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwamw\r\n"
"rkca0lfrHRUfblyy5PgLINvqAN8p/6RriSZLnyMv7FewirhGQCp+vNxaRZdPrUEO\r\n"
"vCCGSwxdVSFH4jE8V6fsmUfrRw1y18gWVHXv00URD0vOYHpGXCh0ro4bvthwZnuo\r\n"
"k0ko0qN2lFXefCfyD/eYDK2G2sau/Z/w2YEympfjIe4EkpbkeBHlxBAOEDF6Speg\r\n"
"68ebxNqJN6nDN9dWsX9Sx9kmCtavOBaxbftzebFoeQOQ64h7jEiRmFGlB5SGpXhG\r\n"
"eY9Ym+k1Wafxe1cxCpDPJM4NJOeSsmrp5pY3Crh8hy900lzoSwpfZhinQYbPJqYI\r\n"
"jqVJF5JTs5Glz1OwMQIDAQABo4IGmDCCBpQwDgYDVR0PAQH/BAQDAgWgMIGgBggr\r\n"
"BgEFBQcBAQSBkzCBkDBNBggrBgEFBQcwAoZBaHR0cDovL3NlY3VyZS5nbG9iYWxz\r\n"
"aWduLmNvbS9jYWNlcnQvZ3Nvcmdhbml6YXRpb252YWxzaGEyZzJyMS5jcnQwPwYI\r\n"
"KwYBBQUHMAGGM2h0dHA6Ly9vY3NwMi5nbG9iYWxzaWduLmNvbS9nc29yZ2FuaXph\r\n"
"dGlvbnZhbHNoYTJnMjBWBgNVHSAETzBNMEEGCSsGAQQBoDIBFDA0MDIGCCsGAQUF\r\n"
"BwIBFiZodHRwczovL3d3dy5nbG9iYWxzaWduLmNvbS9yZXBvc2l0b3J5LzAIBgZn\r\n"
"gQwBAgIwCQYDVR0TBAIwADBJBgNVHR8EQjBAMD6gPKA6hjhodHRwOi8vY3JsLmds\r\n"
"b2JhbHNpZ24uY29tL2dzL2dzb3JnYW5pemF0aW9udmFsc2hhMmcyLmNybDCCA04G\r\n"
"A1UdEQSCA0UwggNBggliYWlkdS5jb22CDGJhaWZ1YmFvLmNvbYIMd3d3LmJhaWR1\r\n"
"LmNughB3d3cuYmFpZHUuY29tLmNugg9tY3QueS5udW9taS5jb22CC2Fwb2xsby5h\r\n"
"dXRvggZkd3ouY26CCyouYmFpZHUuY29tgg4qLmJhaWZ1YmFvLmNvbYIRKi5iYWlk\r\n"
"dXN0YXRpYy5jb22CDiouYmRzdGF0aWMuY29tggsqLmJkaW1nLmNvbYIMKi5oYW8x\r\n"
"MjMuY29tggsqLm51b21pLmNvbYINKi5jaHVhbmtlLmNvbYINKi50cnVzdGdvLmNv\r\n"
"bYIPKi5iY2UuYmFpZHUuY29tghAqLmV5dW4uYmFpZHUuY29tgg8qLm1hcC5iYWlk\r\n"
"dS5jb22CDyoubWJkLmJhaWR1LmNvbYIRKi5mYW55aS5iYWlkdS5jb22CDiouYmFp\r\n"
"ZHViY2UuY29tggwqLm1pcGNkbi5jb22CECoubmV3cy5iYWlkdS5jb22CDiouYmFp\r\n"
"ZHVwY3MuY29tggwqLmFpcGFnZS5jb22CCyouYWlwYWdlLmNugg0qLmJjZWhvc3Qu\r\n"
"Y29tghAqLnNhZmUuYmFpZHUuY29tgg4qLmltLmJhaWR1LmNvbYISKi5iYWlkdWNv\r\n"
"bnRlbnQuY29tggsqLmRsbmVsLmNvbYILKi5kbG5lbC5vcmeCEiouZHVlcm9zLmJh\r\n"
"aWR1LmNvbYIOKi5zdS5iYWlkdS5jb22CCCouOTEuY29tghIqLmhhbzEyMy5iYWlk\r\n"
"dS5jb22CDSouYXBvbGxvLmF1dG+CEioueHVlc2h1LmJhaWR1LmNvbYIRKi5iai5i\r\n"
"YWlkdWJjZS5jb22CESouZ3ouYmFpZHViY2UuY29tgg4qLnNtYXJ0YXBwcy5jboIN\r\n"
"Ki5iZHRqcmN2LmNvbYIMKi5oYW8yMjIuY29tggwqLmhhb2thbi5jb22CDyoucGFl\r\n"
"LmJhaWR1LmNvbYIRKi52ZC5iZHN0YXRpYy5jb22CEmNsaWNrLmhtLmJhaWR1LmNv\r\n"
"bYIQbG9nLmhtLmJhaWR1LmNvbYIQY20ucG9zLmJhaWR1LmNvbYIQd24ucG9zLmJh\r\n"
"aWR1LmNvbYIUdXBkYXRlLnBhbi5iYWlkdS5jb20wHQYDVR0lBBYwFAYIKwYBBQUH\r\n"
"AwEGCCsGAQUFBwMCMB8GA1UdIwQYMBaAFJbeYfG9HBYpUxzAzH07gwBA5hp8MB0G\r\n"
"A1UdDgQWBBSeyXnX6VurihbMMo7GmeafIEI1hzCCAX4GCisGAQQB1nkCBAIEggFu\r\n"
"BIIBagFoAHYAXNxDkv7mq0VEsV6a1FbmEDf71fpH3KFzlLJe5vbHDsoAAAFxObU8\r\n"
"ugAABAMARzBFAiBphmgxIbNZXaPWiUqXRWYLaRST38KecoekKIof5fXmsgIhAMkZ\r\n"
"tF8XyKCu/nZll1e9vIlKbW8RrUr/74HpmScVRRsBAHYAb1N2rDHwMRnYmQCkURX/\r\n"
"dxUcEdkCwQApBo2yCJo32RMAAAFxObU85AAABAMARzBFAiBURWwwTgXZ+9IV3mhm\r\n"
"E0EOzbg901DLRszbLIpafDY/XgIhALsvEGqbBVrpGxhKoTVlz7+GWom8SrfUeHcn\r\n"
"4+9Dn7xGAHYA9lyUL9F3MCIUVBgIMJRWjuNNExkzv98MLyALzE7xZOMAAAFxObU8\r\n"
"qwAABAMARzBFAiBFBYPxKEdhlf6bqbwxQY7tskgdoFulPxPmdrzS5tNpPwIhAKnK\r\n"
"qwzch98lINQYzLAV52+C8GXZPXFZNfhfpM4tQ6xbMA0GCSqGSIb3DQEBCwUAA4IB\r\n"
"AQC83ALQ2d6MxeLZ/k3vutEiizRCWYSSMYLVCrxANdsGshNuyM8B8V/A57c0Nzqo\r\n"
"CPKfMtX5IICfv9P/bUecdtHL8cfx24MzN+U/GKcA4r3a/k8pRVeHeF9ThQ2zo1xj\r\n"
"k/7gJl75koztdqNfOeYiBTbFMnPQzVGqyMMfqKxbJrfZlGAIgYHT9bd6T985IVgz\r\n"
"tRVjAoy4IurZenTsWkG7PafJ4kAh6jQaSu1zYEbHljuZ5PXlkhPO9DwW1WIPug6Z\r\n"
"rlylLTTYmlW3WETOATi70HYsZN6NACuZ4t1hEO3AsF7lqjdA2HwTN10FX2HuaUvf\r\n"
"5OzP+PKupV9VKw8x8mQKU6vr\r\n"
"-----END CERTIFICATE-----\r\n";

#endif
```

编写测试函数文件`mbedtls_x509_test.c`：

```c
/**
 * @brief   X509 Function demo
 * @author  mculover666
 * @date    2020/10/04
*/

#if !defined(MBEDTLS_CONFIG_FILE)
#include "mbedtls/config.h"
#else
#include MBEDTLS_CONFIG_FILE
#endif

#if defined(MBEDTLS_X509_CRT_PARSE_C)

#include <stdio.h>
#include "string.h"
#include "mbedtls/x509_crt.h"
#include "baidu_certs.h"

char buf[4096];

int mbedtls_x509_test(void)
{
    int ret;
   
    mbedtls_x509_crt cert, cacert;

    /* 1. init structure */
    mbedtls_x509_crt_init(&cert);
    mbedtls_x509_crt_init(&cacert);

    /* 2. Parser cacert */
    printf( "\n  . Parse cacert..." );
    
    ret = mbedtls_x509_crt_parse(&cacert, (unsigned char *)baidu_ca_cert, sizeof(baidu_ca_cert));
     if(ret != 0) {
        printf( " failed\n  ! mbedtls_x509_crt_parse cacert returned %d(-0x%04x)\n", ret, -ret);
        goto exit;
    }
    printf( " ok\n" );
    
    /* 2. Cacert parser result */
    printf( "\n  . Cacert parser result..." );
    
    ret = mbedtls_x509_crt_info(buf, sizeof(buf) - 1, "      ", &cacert);
    if (ret < 0) {
        printf("fail! mbedtls_x509_crt_info return %d(-0x%04x)\n", ret, -ret);
        goto exit;
    } else {
        buf[ret] = '\0';
        printf("ok!\r\n");
        printf("crt info has %d chars\r\n", strlen(buf));    
        printf("%s\r\n", buf);
    }
    
    exit:  
    /* 3. release structure */
    mbedtls_x509_crt_free(&cert);
    mbedtls_x509_crt_free(&cacert);
    
    return ret;
}

#endif /* MBEDTLS_RSA_C */
```

### 测试结果

在main.c中声明该测试函数：

```c
extern int mbedtls_x509_test(void);
```

在main函数中调用该测试函数：

```c
/* 10. x509 test */
mbedtls_x509_test();
```

编译、下载、测试结果为：
![img](https://img-blog.csdnimg.cn/20201004131906865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01jdWxvdmVyNjY2,size_16,color_FFFFFF,t_70#pic_center)