#this is a simple project,about return and array 
#在这个项目里二维数组作为存储商品信息的空间

#include <stdio.h>
#include <windows.h> \\just this bit 
int main(){
    SetConsoleOutputCP(65001); \\this is a code to explain code turn to chiese,seem like utf-8 and gbk
    SetConsoleCP(65001);       \\this code use bit about <window.h> 
    int i,n;
    char object[5][20];        \\如果是一维数组的话，只能存入一位字符的大小
    printf("请输入要购买的商品个数\n");
    printf("最多5个\n");
    scanf("%d",&n);
    getchar();                 \\吸收换行符 防止进入下一个scanf函数里面
    printf("输入商品\n");

    if(n <= 5){
        for(int i =0;i<n;i++){
            scanf(" %s",object[i]);
        }
        printf("打印完成，购物清单为\n");
        for(int i=0;i<n;i++){
            printf("%s",object[i]);
        }
    }
    else printf("数据超出，无法添加\n");
    return 0;
}

