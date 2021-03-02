---
title: TLS
date: 2021-03-01 00:00:00
swiper: true
swiperImg: '/medias/14.jpg'
top: true
tags: 乐鑫
---
# 例程

| 文件                                 | 说明          |
| ------------------------------------ | ------------- |
| example/protocals/https_mbedtls/     | mbedtls库用例 |
| example/protocals/https_request/     | esp tls用例   |
| example/protocals/https_x509_bundle/ | esp tls用例   |



# ESP TLS

Esp tls库是乐鑫对mbedlts和wolfssl库的进一步封装，使得对外使用统一的api接口。

默认配置使用mbedtls



## http tls 用例（删减）

```c
esp_tls_cfg_t cfg = {
            .crt_bundle_attach = esp_crt_bundle_attach,
        };

struct esp_tls *tls = esp_tls_conn_http_new(WEB_URL, &cfg);			// 新建https连接

do {
    ret = esp_tls_conn_write(tls,									// 写入http请求
                             REQUEST + written_bytes,
                             strlen(REQUEST) - written_bytes);
    if (ret >= 0) {
        ESP_LOGI(TAG, "%d bytes written", ret);
        written_bytes += ret;
    }
} while(written_bytes < strlen(REQUEST));

do{
    len = sizeof(buf) - 1;
    bzero(buf, sizeof(buf));
    ret = esp_tls_conn_read(tls, (char *)buf, len);					// 接收数据

    /* Print response directly to stdout as it is read */
    for(int i = 0; i < len; i++) {
        putchar(buf[i]);
    }
} while(1);

esp_tls_conn_delete(tls);											// 删除连接
```










# openssl client示例代码（删减）

```c
int ret;
SSL_CTX *ctx;
SSL *ssl;
int sockfd;
struct sockaddr_in sock_addr;
struct hostent *hp;
struct ip4_addr *ip4_addr;

int recv_bytes = 0;
char recv_buf[OPENSSL_EXAMPLE_RECV_BUF_LEN];

const char send_data[] = OPENSSL_EXAMPLE_REQUEST;
const int send_bytes = sizeof(send_data);

ctx = SSL_CTX_new(TLSv1_1_client_method());

sockfd = socket(AF_INET, SOCK_STREAM, 0);
ret = bind(sockfd, (struct sockaddr*)&sock_addr, sizeof(sock_addr));
ret = connect(sockfd, (struct sockaddr*)&sock_addr, sizeof(sock_addr));

ssl = SSL_new(ctx);
SSL_set_fd(ssl, sockfd);
ret = SSL_connect(ssl);
ret = SSL_write(ssl, send_data, send_bytes);
do {
    ret = SSL_read(ssl, recv_buf, OPENSSL_EXAMPLE_RECV_BUF_LEN - 1);
    if (ret <= 0) {
        break;
    }
    recv_buf[ret] = '\0';
    recv_bytes += ret;
    ESP_LOGI(TAG, "%s", recv_buf);
} while (1);

SSL_shutdown(ssl);
SSL_free(ssl);
close(sockfd);
SSL_CTX_free(ctx);
```

